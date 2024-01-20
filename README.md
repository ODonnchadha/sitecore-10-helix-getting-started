
## Getting Started with Sitecore 10 and Helix by Shelley Benhoff

- LEARNING SITECORE:
    - Sitecore Helix. A set of overall design principles and conventions for Sitecore development.
    - Sitecore. CMS. Content and presentation.
    - Base Sitecore installation with a working Web page built using Helix architecture.
        - Web pages: Built beginning with layout. The layout defines the overall structure of the page.
        - Inside of the layout, there are multiple renderings. The rendering are created seperately, so that they can be reused.
            - Rendering are placed within a layout using a placeholder.
            - The rendering content structure is set up using data templates. Each field has its own type.
                - And then Sitecore generates the correct HTML output for each data type.
        - Layouts, Renderings, and Data Templates need to be built using the recommended architecture and practicles.
        - Data templates will act as a model which will be bound to renderings to display the values from the fields in Sitecore.

- CONFIGURING A DEVELOPMENT ENVIRONMENT FOR SITECORE:
    - Installing SQL Server: Install 'new, stand-alone version.' Install 'Database Engine Services' and 'Analysis Services.'
        - Choose 'Mixed Mode.' Add current user as an admin. Reboot. Install SSMS. Restart.
    - Installing Docker: Docker helps to simplify the development workflow by using a containerized approach.
        - Windows 10? Install Docker Desktop. 
        - Docker Desktop installation. Configuration:
            1. Enable Hyper-V Windows Features
            2. Install required Windows components for WSL 2
        - Within system tray for Docker, ensure "Switch to Windows containers." 
        - Modify settings - Docker Engine:
            ```javascript
                "dns": ["8.8.8.8"]
            ```
        - This points to Google's public DNS, which will assure that DNS lookups do not fail within Docker images.
        - Do not instal the Linux kernel. BIOS Virtualization turned on.  Close and restart.
        - Switch to Windows containers. (Default is Linux.) Edit settings. Modify configuration:
        - Ensure DNS lookups will not fail. Command prompt:
            ```javascript
                docker version
            ```
        - Ensure that Windows is set for both Docker client and Docker server.

- INSTALLING SITECORE:
    - [Sitecore developer portal](https://dev.sitecore.net/)
    - Developer license. Developer trial has been discontinued.
    - Sitecore Install Assistant (SIA.) On premises deployment: 
        - Good for installing local dev environment and installtion with Dev or QA environments.
        - Do not install for: multi-server environments. Production servers.
        - Graphical setup package for XP Single. Unpackage.
            - Modify setup.exe.config SqlServer, SqlAdminUser, SqlAdminPassword and {prefix} at SitecoreSiteName,
        - Install prerequisites.
        - SOLR. Search.
        - denoted-prefix.dev.local/sitecore
    - Sitecore Installation Framework (SIF.)
        - 5.1 PowerShell version or greater.
        ```javascript
            $PSVersionTable.PSVersion
            Set-ExecutionPolicy Unrestricted
        ```
        - Install for: Customized single-server installations. Multi-server. (CM. CD. SQL. SOLR.)
        - Inspect via IIS. Identity Server. Public site. Content management and presentation.
        - XConnect. Service layer for XDB databases. Set of SQL Server DBs.
        - Inspect via folder structure. wwwwroot. Site name. 
            - Logs: App_Data\logs. "log" general log information. Bamed via function. General = Log.
            - Connection strings: App_Config\ConnectionStrings.log
            - SQL:
                - core: Sitecore interface settings. e.g.: Custom menu options.
                - security: Identity server for authentication.
                - master: All content. Both published and unpublished. Work in progress.
                - web: The live site. Only published content.
            - SOLR:
                - Dashboard. Search and indexing provider. Cores: Master and Web.
                - Search indexes for all content. One folder per core.
        - MAKE A BACKUP OF THE WEB ROOT AFTER INNITIAL INSTALL.
        - Docker: Ensure the following ports are not being used: 443, 8079, 8081, 8984, 14330.
            - Stop IIS. [GitHub Docker Examples](https://github.com/sitecore/docker-examples)
            ```javascript
                iisreset /stop
            ```
            - INIT script. Download all required images. Create a default network and a container for each configured service.
            ```javascript
                docker-compose up -d
                docker ps
                docker-compose logs -f --tail 20
            ```
            - xp0id.localhost/sitecore. Incognito mode. SSMS connection: localhost,14330
            - Ensure Docker Desktop is running in Windows container mode.

- SETTING UP A SITECORE MVC VISUAL STUDIO PROJECT USING HELIX:
    - Create folder structure outside of the Web root. New project: ASP.NET Web Application (.NET Framework)
        - Uncheck 'Place solution and project in the same directory.' Framework 4.8. Create an empty project.
    - Project: Presentation. Including layouts and page types. Project references feature and foundation.
        - Create Website folder. ASP.NET Web Application. Select MVC.
            - Sitecore.Kernel
            - Sitecore.MVC
    - Feature: Components and pieces of functionality. Feature references foundation.
        - PageContent. MVC project template. Build action: None. And 'Do not copy.'
        - Add [Sitecore Package Source](https://sitecore.myget.org/F/sc-packages/api/v3/index.json)
            - Sitecore.Kernel
            - Sitecore.MVC
    - Foundation: House projects that are at the core of the project. Configuration and third-party frameworks.
        - Configuration: Create a "code" folder. Layer. Project. "Code" folder. Contains the Sitecore Web.config root.
        - App_Config: Add ConnectionStrings.config.
        - Properties. "Copy if newer."
    - Create an /src folder right where the project solution exists. And the create the three folders /src/
    - Deploy to a Docker container. Local file deploys. Use a "shared" volume.
        ```javascript
            docker-compose down
            docker-compose up -d
            docker ps
        ```
        - Base image. Tooling image. Local deploy path. Docker container web root receivesa files after they are pushed to a different Docker location.
        - VS Code for managing Docker development. (Withn a Pluralsight plug-in.)
        - Deploying from VS Solution to Docker container:
            - Use PowerShell to inspect the file system
                ```javascript
                    docker exec -it sitecore-xp0_cm_1 powershell
                ```
            1. Add the Sitecore Docker Tools Module
            2. Create local folders for deploy amd logs
            3. Use Docker volumes to map local folders to the container
            4. Create a Docker publish profile for your VS solution.
            5. Validate viewing the file system using Powershell *inside* the container.

- WORKING WITH SITECORE LAYOUTS, RENDERINGS, AND DATA TEMPLATES:
    - Building a Sitecore Page Type and Layout:
        - MVC. Rebuild websorms page.
        - sitecore/Layout/Layouts/Project.
            - Select "Layout Folder." Name "Website." Within folder "Website," click "Layout."
                - Name it "Default" and path to our file.
        - Experience editor "Proview" mode. For quick debugging.
        - Page Type Template: Combines presentation with the content structure.
            - Create a page type for every variation on the Website. 
                - e.g.: Home page. Content detail page.
                    - May use the sme layout with different components or renderings.
                    - Two seperate page types in Sitecore.
            - A Layout is required to set the presentation. A layout is the frame of a house.
            - Template standard values set the default values for any item in Sitecore that uses that template.
    - Publishing a Sitecore Website: From master database to web database.
        - Smart Publish. Difference between source and target. Republish. Incremental Publish.

    - Working with Template Inheritance:
        - "Body copy view rendering." Title. Body copy. Via a data template. Editable content.
            - Body Copy ()=> Body Copy Content. Title => Single-Line Text. Body Copy => Rich Text.
            - Template inheritance. Same fields. Always inherit from which that is defined.
            - Within Builder, create a new field section "Body Copy Content." Used to group similiar fields.
            - Body Copy Content:
                - Title: Single-Line Text
                - Body Copy: Rich Text
                - NOTE: We are using spaces within the names of the templates, field groups, and fields.
                - Why? User we see these names while editing content. Easy to read and understand.
                - PageType templates should never include fields of their own. 
                    - They should always inherit from multiple data templates.
                    - Define in *one* template and then inherit.
    - Developing the body copy view rendering:
        ```javascript
            @model Sitecore.Mvc.Presentation.RenderingModel
            using Sitecore.Mvc
            <div>
                @Html.Sitecore().Field("Title")
                @Html.Sitecore().Field("Body Copy")
            </div>
        ```
        ```javascript
            @Html.Sitecore().Placeholder("main)
        ```
        - With 'main' as "Add to body" within renderings/controls. You can publish from the experience editor.
        - View rendering: @model Sitecore.Mvc.Presentation.RenderingModel
        - Placeholder: @Html.Sitecore().Placeholder("x")
        - Data Template: Content Definition.
        - Fields: @Html.Sitecore().Field("y")
        - NOTES:
        - A view rendering in Sitecore uses the rendering model to bind content to presentation.
        - Sitecore rendering model enables us to obtain the values of the fields, the content item, that are currently loaded on the page.
        - Add a placeholder to the default layout. This tells Sitecore where to render the HTML for a rendering.
        - And then add the body copy rendering to the default page type we told Sitecore to add it to the "main" placeholder.
        - The data template is where we defined the content to support the presentation in the view rendering.
        - Data fields are added to the data template depending on the needs of the rendering. e.g.: title. body copy.
        - Field types allow to define a field's purpose.
        - Group fields and templates that pertain to the same business logic. Then use template inheritance.
    - Creating a Global Datasource:
        - Hero Image template. With field sections. Hero Image and Hero Logo. All with the backing type of 'Image.'
            - Keep global outside of any home.
    - Working with Images in the Media Library.
        ```javascript
            @model Sitecore.Mvc.Presentation.RenderingModel
            using Sitecore.Mvc
            @{
                Sitecore.Data.Fields.ImageField heroImageField = Model.Item.Fields["Hero Image"];
                string heroImageUrl = Sitecore.Resources.Media.MediaManager.GetMediaUrl(heroImageField.MediaItem);
            }
            <div id="header" style="background: white url('@heroImageUrl') no-repeat; background-size: 1170px 402px;">
                @Html.Sitecore().Field("Hero Logo", Model.Item, new { @id = "scLogo" })
            </div>
        ```
        - Create new view rendering. And add the newly-created rendering to the layout. And remember to set the datasource.
        - Media Library: Media aasset storage.
        - Image Field: @Html.Sitecore().Field("Hero Logo", Model.Item, new { @id = "scLogo" })
        - Image Url: Sitecore.Resources.Media.MediaManager.GetMediaUrl(ImageField.MediaItem);
        - Content Item versus Datasource.

- STUDYING SITECORE:
    - Helix basics: 
        1. Configure a development environment.
        2. Install Sitecore.
        3. Set up a Sitecore MVC VS Project using Helix.
        4. Build pages using layouts, renderings, and data templates.
    - Resources:
        - Building a Sitecore Helix Website:
            1. Configuring a Sitecore Helix Website.
            2. Creating Sitecore Helix renderings.
            3. Editing Content in the Sitecore Experience editor.
            4. Working with media types and the Sitecore media library.
            5. Discussing a Sitecore production environment. (And serialization.)
    - [Sitecore Documentation](https://doc.sitecore.com/)
    - [Sitecore Stack Exchange](https://sitecore.stackexchange.com/)
    - [Sitecore Community](https://community.sitecore.com/community)
    - [Jeremy Davis](https://blog.jermdavis.dev/)
    - [Peter Prochazka](https://tothecore.sk/)

- WORKING WITH SITECORE UPDATES:
    - Update information: See documentation. Release notes in addition to known issues. 
        - Major version update?  Stand up a brand new implementation and migrate.
    - Sitecore 10.x: 
        - SQL versus MongoDB (personalization and reporting.) XDB migration tool.
        - Update NuGet packages.
        - Sitecore.UpdateApp too: command-line. Used to update Core, Master, and Web databases.
        - Content supporter now supports a table view.