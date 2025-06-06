# Initialize

Now that you have `invenio-cli` [installed](cli.md), we will use it to scaffold a new instance of InvenioRDM.

## Initialize your project

First, we need to create the project - the necessary files and folders for your InvenioRDM instance. You'll want to commit
the generated project into a version control system, as it will contain your repository and its customizations. If you're working
on an existing repository you can skip the scaffolding step, as it only has to be done once (and clearly has been).

This documentation describes `invenio-cli` version 1.6.0. Other versions may have different data requests.

The CLI will require the following data:

- **Project name**: Title of your project with space allowed (project name for humans)
- **Project short name**: Hyphenated and lowercased title (project name for machines)
- **Package name**: Snake cased and lowercased title (project name for machines)
- **Project website**: URL where the project will be deployed
- **Author name**: Your name or that of your organization
- **Author email**: Email for communication
- **Year**: The current year
- **Database**: PostgreSQL (default)
- **Search**: OpenSearch 2 (default)
- **Storage backend**: Local file system (default) or in a S3-like backend. If S3 is chosen a MinIO container is provided.
- **Development tools**: `Yes` if you want to add extra development tools (e.g. OpenSearch Dashboard) to your instance.
- **Site code**: `Yes` if you want to add custom code to your instance.
- **Reduced vocabularies**: `Yes` builds a full set of language, license and name vocabularies. `No` uses a reduced set for faster setup.

It will also generate a test private key which is needed for SSL support in the development server.

Let's do it! Pressing `[Enter]` selects the default option in brackets `[]`.

=== "Latest release (default)"

    ```shell
    invenio-cli init rdm
    ```

=== "Specific version"

    ```shell
    invenio-cli init rdm -c <version>
    # e.g:
    invenio-cli init rdm -c v12.0
    # for pre-release (InvenioRDM development branch)
    invenio-cli init rdm -c master
    ```

The current InvenioRDM release (for production systems) is ``v12.0``

```console
Initializing RDM application...
Running cookiecutter...
project_name [My Site]:
project_shortname [my-site]:
package_name [my_site]:
project_site [my-site.com]:
author_name [CERN]:
author_email [info@my-site.com]:
year [2023]:
Choose from 1 [1]:
Select database:
1 - postgresql
Choose from 1 [1]:
Select search:
1 - opensearch2
Choose from 1, 2 [1]:
Select file_storage:
1 - local
2 - S3
Choose from 1, 2 [1]:
Select development_tools:
1 - yes
2 - no
Choose from 1, 2 [1]:
Select site_code:
1 - yes
2 - no
Choose from 1, 2 [1]:
Select use_reduced_vocabs:
1 - no
2 - yes
Choose from 1, 2 [1]:
```

## Project structure

You can now inspect the generated project structure:

```shell
cd my-site
ls -a1
```

```console
.
..
.dockerignore
.gitignore
.invenio
.invenio.private
Dockerfile
Pipfile
README.md
app_data
assets
docker
docker-compose.full.yml
docker-compose.yml
docker-services.yml
invenio.cfg
logs
static
templates
```

Following is an overview of the generated files and folders:

| Name | Description |
|---|---|
| ``Dockerfile`` | Dockerfile used to build your application image. |
| ``Pipfile`` | Python requirements installed via [pipenv](https://pipenv.pypa.io) |
| ``app_data/`` | Application data for e.g. vocabularies. |
| ``assets/`` | Web assets (CSS, JavaScript, LESS, JSX templates) used in the Webpack build. |
| ``docker/`` | Example configuration for NGINX and uWSGI for running InvenioRDM. |
| ``docker-compose.full.yml`` | Example of a full infrastructure stack (DO NOT use in production!) |
| ``docker-compose.yml`` | Backend services needed for local development. |
| ``docker-services.yml`` | Common services for the Docker Compose files. |
| ``invenio.cfg`` | The Invenio application configuration. |
| ``logs/`` | Log files. |
| ``static/`` | Static files that need to be served as-is (e.g. images). |
| ``templates/`` | Folder for your Jinja templates. |
| ``.invenio`` | Common file used by Invenio-CLI to be version controlled. |
| ``.invenio.private`` | Private file used by Invenio-CLI *not* to be version controlled. |

### Notes and known issues

- You may be prompted with `You've downloaded /home/<username>/.cookiecutters/cookiecutter-invenio-rdm before. Is it okay to delete and re-download it? [yes]:`. Press `[Enter]` in that case. This will download the latest cookiecutter template.

- Some OpenSSL versions display an error message when obtaining random numbers, but this has no incidence on functionality.
