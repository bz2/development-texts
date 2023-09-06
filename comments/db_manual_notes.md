# Notes from manual db backup/restore in Azure

Using ssh to get to the app service (which is allowed to connect to the db).

    https://<appservicename>.scm.azurewebsites.net/webssh/host


## Fetching tools

Getting `pg_dump` or `pg_restore` tool:

    apt-get update
    apt-get install postgresql-client-common postgresql-client-14

Getting python library for Azure storage client:

    pip3 install azure-storage-blob

Not required with new image that already includes this.


## Backup

Creating the dump of the database:

    pg_dump -Fc -Ox --quote-all-identifiers "${DATABASE_URL}" > db.dump

Operations to upload the dump (using `python3`):

    >>> from azure.storage.blob import BlobClient
    >>> conn_str = "..."  # from storage access
    >>> container = "..."  # should be name of a private bucket
    >>> c = BlobClient.from_connection_string(conn_str, container, "db.dump")
    >>> with open('db.dump', 'rb') as f: c.upload_blob(f.read())


## Restore

Operations to download the dump (using `python3`):

    >>> from azure.storage.blob import BlobClient
    >>> conn_str = "..."  # take from storage access
    >>> container = "..."  # should be name of a private bucket
    >>> c = BlobClient.from_connection_string(conn_str, container, "db.dump")
    >>> r = c.download_blob()
    >>> with open('db.dump', 'wb') as f: f.write(r.content_as_bytes())

Restoring the dump to the database:

    pg_restore -cO -d "${DATABASE_URL}" db.dump


## Discoveries

### Password encoding

As the db password contained a `&` but the password was not encoded in the
`postgresql://` url. This gave the following error:

    pg_restore: error: missing key/value separator "=" in URI query parameter: "..."

The fix was to percent-encode the password with `url.parse.quote(pass)` and fix the url. Also updated the config in the app service, the bicep file that created it should be updated to correctly quote the value on creation.


### Role missing on restore

Event though the backup was created with the `--no-owner` flag, the restore spewed complaints in the form:

    pg_restore: error: could not execute query: ERROR:  role "vm_admin" does not exist

Adding the `-O` (`--no-owner`) flag resolved it, and the `-c` (`--clean`) flag to clear out the previously restored data.
