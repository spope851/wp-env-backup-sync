# WordPress Environment Backup Sync

This action backs up and syncs a WordPress installation in Hostinger from a source environment to a target environment.

## Usage

```yaml
name: Sync WordPress Environments
on:
  workflow_dispatch:
    inputs:
      source_env:
        description: 'Source environment'
        required: true
        type: choice
        options:
          - development
          - staging
          - release
      target_env:
        description: 'Target environment'
        required: true
        type: choice
        options:
          - staging
          - production

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Sync WordPress environments
        uses: spope851/wp-env-backup-sync@v1
        with:
          source_env: ${{ github.event.inputs.source_env }}
          target_env: ${{ github.event.inputs.target_env }}
          base_path: '/home/u123456/domains/example.com/public_html'
          domain: 'example.com'
          ssh_host: '${{ secrets.SSH_HOST }}'
          ssh_username: '${{ secrets.SSH_USERNAME }}'
          ssh_private_key: '${{ secrets.SSH_PRIVATE_KEY }}'
          ssh_known_hosts: '${{ secrets.SSH_KNOWN_HOSTS }}'
          ssh_key_type: 'id_rsa'
          ssh_port: '22'
          encryption_password: '${{ secrets.ENCRYPTION_PASSWORD }}'
          github_token: '${{ secrets.GITHUB_TOKEN }}'
```

### Inputs

- `source_env` (required): Source environment name (e.g., release, staging, development)
- `target_env` (required): Target environment name (e.g., staging, production)
- `base_path` (required): Base path for WordPress installations
- `domain` (required): Base domain for the WordPress sites
- `ssh_host` (required): SSH host for the server
- `ssh_username` (required): SSH username
- `ssh_private_key` (required): SSH private key for authentication
- `ssh_known_hosts` (required): SSH known hosts configuration
- `ssh_key_type` (required): SSH key type (e.g., id_rsa)
- `ssh_port` (required, default: '22'): SSH port
- `encryption_password` (required): Password for encrypting backup files
- `github_token` (required): GitHub token for pushing changes
### What it does

1. Activates maintenance mode on the target environment
2. Syncs wp-content directory (themes, plugins, uploads, languages, mu-plugins)
3. Creates a backup of the target database
4. Exports the source database with URL replacements
5. Imports the modified source database to the target
6. Clears various WordPress caches
7. Creates encrypted backups of both the original target database and the migration
8. Stores the encrypted backups in the repository
9. Deactivates maintenance mode

### Prerequisites

1. SSH access to your Hostinger server
2. WordPress CLI installed on the server
3. Required GitHub repository secrets:
   - `SSH_HOST`
   - `SSH_USERNAME`
   - `SSH_PRIVATE_KEY`
   - `SSH_KNOWN_HOSTS`
   - `ENCRYPTION_PASSWORD`

### Assumptions

- WordPress installation structure:
  - `public_html/` (production)
  - `public_html/staging/` (staging)
  - `public_html/development/` (development)
  - `public_html/release/` (release)
- Environment domains follow same naming convention as the environment directories, e.g. `example.com`, `staging.example.com`, `development.example.com`, `release.example.com`

### Release Versioning

This action uses semantic versioning. The version is set in the `action.yml` file and is incremented with each release. The following tags are maintained:

- `vX` - Latest release
- `vX.Y.Z` - Specific release

To create a new release, use the following commands:

``` bash
changie batch patch|minor|major
changie merge
git tag -a vX.Y.Z -m "vX.Y.Z"
git push origin vX.Y.Z
git tag -fa vX -m "Update vX tag"
git push origin vX --force
```

### Contributing

If you have any suggestions or improvements, please create an issue or submit a pull request.
