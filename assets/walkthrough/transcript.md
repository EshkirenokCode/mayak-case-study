# Walkthrough transcript

[Back to the case study](../../README.md) · [Watch the video](MAYAK-Walkthrough.mp4)

The English walkthrough uses invented sample data and a visual reconstruction of the implemented workflow. It is not a production screen recording.

## MAYAK

Construction work crosses sites, office teams and documents. MAYAK gives those handoffs a shared record. I built the product, its access model and the infrastructure that keeps it running.

## The project is the common context.

Tasks, photos, timesheets, materials, contracts and purchase requests are organised around construction projects. Materials use a movement history: receipts, write-offs and transfers explain the balance instead of replacing it.

## Follow request 042.

A site manager requests plywood and accessories, with separate items, a required date and an approver. Approval hands it to procurement. Each material item needs invoice files before finance can review it.

## The decision belongs to each file.

Procurement keeps supplier alternatives in named groups, with comments explaining the options. Finance selects the actual files to use. Unselected alternatives remain visible in history rather than disappearing.

## What happens when finance clicks approve?

The SQL routine locks the request, then checks the actor, company and current stage. It rejects foreign files and incomplete selections: every material item must have a chosen invoice. File decisions, request status, the register entry and the event commit in one database transaction.

## Two responsibilities, one request.

Procurement receives the approved request to finish the order. The dispatcher receives its chosen invoices. Supply can close its work independently. The register entry remains visible and turns green when registered.

## The route supports corrections.

A previous actor can recall their handoff; revision can return work to an earlier permitted stage. A direct-invoice route goes straight to finance and dispatch. It does not create a supply completion task.

## Authority and responsibility are separate.

Rank controls managerial authority. Supply, finance and dispatcher duties can be combined without raising rank. Company and project rules constrain normal queries through RLS. Privileged routines perform their own checks.

## A submitted task is not accepted work.

In the task module, submitting work moves it to review. The task author accepts it or returns it for revision. The status selector cannot bypass acceptance. If the author is deleted, management can resolve the review.

## The request still belongs to the user.

Next.js server actions use the user session to call self-hosted Supabase through its HTTP gateway. Auth handles sessions; PostgreSQL enforces data rules; Storage keeps files. Explicit admin paths are separate. For invoice downloads, the server checks file access before streaming from a 60-second signed URL.

## Designed for the field, not just the office.

On phones, photos are resized before upload. Smaller files reduce bandwidth without requiring a separate mobile app. MAX pairing uses a one-time code stored as a hash. Notifications link the next person to the request. Delivery is best-effort: a messaging failure must not become the source of truth for the workflow.

## Recovery is part of the implementation.

Docker and Nginx run the service. Encrypted offsite snapshots preserve the database, files and configuration. I tested recovery in a separate directory and database, including the file attributes needed for correct downloads.

## My contribution.

I translated operating rules into workflows, built the interface and backend, and implemented the access model. I also handled deployment, server migration, backups and fixes driven by people using MAYAK at work.
