
## Getting Started with Sitecore 10 and Helix by Shelley Benhoff

- LEARNING SITECORE:
    - Sitecore Helix. A set of overall design principles and conventions for Sitecore development.
    - Sitecore. CMS. Content and presentation.

- CONFIGURING A DEVELOPMENT ENVIRONMENT FOR SITECORE:
    - Installing SQL Server: Install 'new, stand-alone version.' Install 'Database Engine Services' and 'Analysis Services.'
        - Choose 'Mixed Mode.' Add current user as an admin. Reboot. Install SSMS. Restart.
    - Installing Docker: Docker helps to simplify the development workflow by using a containerized approach.
        - Windows 10? Install Docker Desktop. BIOS Virtualization turned on. Docker Desktop installation. WSL 2 enabled. Close and restart.
        - Switch to Windows containers. (Default is Linux.) Edit settings. Modify configuration:
            ```javascript
                "dns": ["8.8.8.8"]
            ```
        - Ensure DNS lookups will not fail. Command prompt:
            ```javascript
                docker version
            ```

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
        - Inspect via IIS. Identity Server. Public site. Content management and presentation. XConnect. Service layer for XDB databases.
        - Inspect via folder structure. wwwwroot. Site name. 
            - Logs: App_Data\logs. "log" general log information.
            - Connection strings: App_Config\ConnectionStrings.log
            - SQL:
                - core: Sitecore interface settings. e.g.: Custom menu options.
                - security: Identity server for authentication.
                - master: All content. Both published and unpublished. Work in progress.
                - web: The live site.
            - SOLR:
                - Dashboard. Search and indexing provider. Cores: Master and Web. Search indexes for all content. One folder per core.
        - Docker: Ensure the following ports are not being used: 443, 8079, 8081, 8984, 14330.
            - Stop IIS. [GitHub Docker Examples](https://github.com/sitecore/docker-examples)
            - INIT script. Download all required images. Create a default network and a container for each configured service.
            ```javascript
                docker-compose up -d
                docker ps
                docker-compose logs -f --tail 20
            ```
            - xp0id.localhost/sitecore. Incognito mode. SSMS connection: localhost,14330
            - Ensure Docker Desktop is running in Wiondows container mode.

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
        - Page Type Template: CombinespPresentation and content.
            - Layout: Presentation.
            - Template standard values: Default values.
    - Publishing a Sitecore Website:
        - Smart Publish. Difference between source and target. Republish. Incremental Publish.
    - Working qwith Template Inheritance:
        - "Body copy view rendering." Title. Body copy. Via a data template.
            - Body Copy ()=> Body Copy Content. Title => Single-Line Text. Body Copy => Rich Text.
            - Template inheritance. Same fields. Always inherit from which that is defined.
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
        - Data Template: COntent Definition.
        - Fields: @Html.Sitecore().Field("y")
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