# Running and moving MAYAK

[Back to the case study](../README.md)

I look after the server as well as the application. Moving MAYAK to a VPS made several of the less visible dependencies very obvious.

## Deployment

The deployment flow backs up the database before updating code, applying migrations, building the application image and restarting the service. Database migrations have an application ledger so already-applied changes are not run again as if they were new.

A successful application build is only one check. File access, identity/session behaviour, database policies and workflow permissions also depend on the running stack's configuration.

## Moving the service

The migration required more than a database dump:

- Database roles, ownership, grants and application records.
- Storage file contents and extended attributes.
- Supabase and application environment configuration.
- Compose files and database bootstrap/custom configuration.
- HTTPS certificates and reverse-proxy settings.
- Scheduled jobs and offsite credentials.

I first restored and tested the new instance. For the final handoff, writes to the old application services were stopped, a final database copy and file synchronization were taken, and traffic was redirected before the DNS change completed.

A useful failure during this process was that the application records loaded but photographs and invoice files did not. Restoring storage metadata as well as the file bytes was part of bringing downloads and previews back correctly.

## Versioned recovery snapshots

The current backup job captures a full database dump with ownership and grants, a separate roles dump, original Storage files and attributes, application/Supabase configuration and proxy settings. The proxy's SQLite configuration is copied through SQLite's backup API rather than treated as an arbitrary live file.

Restic encrypts and deduplicates the snapshots. Successful local snapshots are copied to a Google Drive repository using a dedicated OAuth client. The copy step confirms that the latest successful local snapshot exists remotely; an error does not update the success timestamp.

The retention policy keeps 14 daily, 8 weekly and 6 monthly recovery points. Those categories overlap. Unchanged data is deduplicated rather than copied as a fresh full archive each time.

## Keeping incomplete work out of the cloud copy

A snapshot starts with a temporary tag. It is promoted to the successful tag only after the backup command succeeds. Offsite copying selects successful snapshots, so an interrupted or incomplete capture is not silently treated as the latest valid recovery point.

The job is protected against overlapping runs, resource usage is limited, and logs are rotated. The encryption password and recovery configuration also have a separate off-server copy; a password stored only inside its own encrypted repository cannot help recover that repository.

## The restore check

I restored the offsite snapshot into a separate target directory and restored its database into a separate test database. The checks included database records, file contents, extended attributes and proxy SQLite integrity. The database restore ran with error-stop behaviour rather than ignoring SQL failures.

I used a separate directory and test database for the rehearsal. I didn't replay the roles dump into the live cluster: roles are cluster-wide, so that would have changed production. I haven't tested a full bare-metal recovery.

## Things to keep watching

- One VPS remains a single failure domain. Offsite recovery is not automatic high availability.
- The live file capture and transactional database dump are not a single atomic cross-system snapshot.
- Access to cloud storage can be revoked or disrupted, so last-success status and periodic restore checks still matter.
- Backups do not prove that all application-level workflows are correct.
