# Database Backup Utility

A secure PostgreSQL backup utility that supports local storage and AWS S3 destinations with optional encryption.

## Features

- PostgreSQL database backups using pg_dump
- S3 and local file storage destinations
- GPG encryption support
- Compression with gzip
- AWS Lambda deployment support
- Restore functionality

## Installation

### Requirements

- Python 3.13+
- PostgreSQL client tools (pg_dump, psql)
- GPG (for encryption)
- AWS CLI configured (for S3 destinations)

### Setup

1. Clone the repository
2. Install dependencies:
   ```bash
   uv sync
   ```

## Configuration

### Environment Variables

Set the following environment variables:

```bash
# Database connection
export DB_HOST=localhost
export DB_PORT=5432
export DB_USER=backup_user
export DB_PASSWORD=your_password
export DB_NAME=your_database

# S3 destination (optional)
export S3_BUCKET=your-backup-bucket
export AWS_REGION=us-east-1

# Encryption (optional)
export ENCRYPTION_ENABLED=true
export GPG_RECIPIENT=admin@example.com
```

### Configuration File (Legacy)

Alternatively, you can use a `config.yaml` file:

```yaml
source:
  type: postgresql
  host: localhost
  port: 5432
  user: backup_user
  password: your_password
  dbname: your_database

destination:
  type: s3
  region: us-east-1
  bucket: your-backup-bucket
  key: postgres/backup.sql

encryption:
  enabled: true
  recipient: admin@example.com
```

## Usage

### Basic Backup

Using environment variables (recommended):
```bash
python src/main_refactored.py
```

Using configuration file:
```bash
python main.py
```

### AWS Lambda

Deploy the Lambda function using `lambda_handler.py`:
```bash
# Package dependencies
pip install -r requirements.txt -t package/

# Create deployment package
zip -r backup-function.zip lambda_handler.py package/
```

### Restore

To restore a backup:
```bash
python restore.py
```

Follow the interactive prompts to select and restore a backup.

## File Structure

```
├── main.py                 # Original backup script
├── lambda_handler.py       # AWS Lambda version
├── restore.py              # Restore utility
├── config.yaml             # Configuration file
├── sources/
│   └── postgresql.py       # PostgreSQL backup logic
├── destinations/
│   ├── s3.py              # S3 upload
│   └── local.py           # Local file storage
└── src/                   # Improved architecture
    ├── main_refactored.py # Secure main script
    ├── core/              # Core interfaces
    ├── security/          # Security utilities
    └── performance/       # Performance optimizations
```

## Security

- Never commit passwords or API keys to version control
- Use environment variables for sensitive configuration
- The improved architecture in `src/` includes input validation and secure credential handling
- GPG encryption protects backups at rest

## Testing

Run the test suite:
```bash
# Install development dependencies
uv sync --extra dev

# Run tests
pytest

# Run with coverage
pytest --cov=src
```

## Troubleshooting

### Common Issues

**pg_dump not found:**
Install PostgreSQL client tools and ensure they're in your PATH.

**AWS credentials not found:**
Configure AWS CLI or set AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY environment variables.

**GPG encryption fails:**
Ensure GPG is installed and the recipient's public key is imported:
```bash
gpg --import recipient-public-key.asc
```

**Permission denied errors:**
Check file permissions and ensure the backup user has appropriate database privileges.

## License

MIT License - see LICENSE file for details.
