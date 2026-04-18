# Email Validator

Django application for uploading CSV files, validating the email addresses they contain, and downloading either the original file or a processed copy with a validation result column.

## What the app does

- Accepts CSV uploads through a simple web UI.
- Stores uploaded files in the Django database and media directory.
- Validates each value in the `email` column using `py3-validate-email`.
- Writes a processed CSV named `processed_<original-file-name>`.
- Lets users download or delete uploaded files from the index page.

## Stack

- Django 3.2
- SQLite by default
- Pandas for CSV handling
- `py3-validate-email` with format, blacklist, DNS, and SMTP checks

## Project layout

- `app/`: Django project configuration
- `validator/`: upload, process, download, and delete logic
- `templates/`: server-rendered UI
- `media/`: runtime output directory for uploaded and processed files

## Input expectations

- Uploads must be `.csv` files.
- The CSV is expected to contain a column named `email`.
- Uploaded filenames must be unique in the database.
- The model currently limits stored filenames to 20 characters.

## Setup

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```

Open `http://127.0.0.1:8000/` after the server starts.

## How processing works

1. Upload a CSV file from the home page.
2. Click `Process` for the uploaded file.
3. The app reads the `email` column, validates each address, and appends a `valid_status` column.
4. Click `Download` to retrieve the processed CSV.

## Validation details

The current validator checks:

- email syntax
- blacklist status
- DNS records
- SMTP reachability

This makes processing more accurate than regex-only validation, but also slower and dependent on external mail infrastructure.

## Limitations

- Processing is synchronous and happens in the request cycle.
- Large uploads are rejected, and there is no background job queue.
- SMTP-based validation can be slow or inconsistent depending on the target mail server.
- The app uses `DEBUG = False` in settings, but the secret key is hard-coded and should be moved to environment configuration for real deployments.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT. See [LICENSE](LICENSE).
