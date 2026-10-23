# Access rules

[Back to the case study](../README.md)

I split access into rank, duties and data scope. Procurement is a good example of why: someone needs to handle supplier invoices without gaining authority over the site team.

## 1. Rank: managerial authority

| Internal rank | Product role | Intended responsibility |
|---|---|---|
| 1 | Crew | Assigned work and site contributions. |
| 2 | Engineer / technical staff | Operational tasks and project records. |
| 3 | Site manager | Site-level management and permitted team administration. |
| 4 | Management | Company-wide management actions. |

The external client role sits outside this internal ladder. It is not treated as another employee rank in this case study.

Team-management checks also compare the actor's and target's rank. A site manager cannot gain control of management accounts by using a lower-level team action.

## 2. Duties: department responsibilities

Supply, finance and dispatcher are duties attached to a profile. They can be combined, and they do not raise the profile's rank.

For example, selecting invoice files requires the finance duty. Registering chosen invoices is dispatch work. These permissions should not imply authority to manage the entire project team.

## 3. Scope: which company's and projects' data?

The session belongs to a profile and company. PostgreSQL policies constrain ordinary data access using company state and the applicable project-visibility rules. Management's visibility is broader than that of an employee assigned to particular projects.

A valid company or role check does not automatically prove access to a particular record. The record's company and project matter too.

## Where the checks run

The interface shows the actions that make sense for the viewer. Server actions validate input and permissions so errors are understandable. User-scoped database paths are still subject to row-level security.

Some operations intentionally use a privileged client or `SECURITY DEFINER` routine. Those paths cannot rely on RLS in the same way. They must establish the actor and validate the company, duty, ownership or specific transition explicitly. The finance-approval routine is one such example.

The platform activity report is another distinct boundary. Its loader and export path check the configured platform administrator. Being a company manager is not sufficient to access this cross-company reporting capability.

## Task acceptance

Task acceptance is ownership-based, not simply rank-based. The assignee submits work for review; the task author accepts or returns it. A manager assigned to someone else's task does not gain acceptance rights merely by having a higher rank.

The normal status selector excludes the review and accepted states. If the author has been deleted and the author reference becomes null, management can resolve the otherwise stranded review.

## MAX account pairing

The site issues a random connection code. The database stores a hash, not the public code. Redemption checks expiry and whether it has already been used, then links the MAX identity and consumes the token within a database operation.

This identifies the account to notify; it does not grant the MAX user new application permissions. The message links back to MAYAK, where the normal session and access rules apply.

## Security review

I haven't commissioned a penetration test. The boundaries described here are the ones implemented in the code. Some server operations use explicit privileged checks, activity logging covers recorded events, and a sent notification is not a read receipt.
