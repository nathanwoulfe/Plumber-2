### Overview

Plumber extends Umbraco's standard publishing model to allow creation of multi-stage approval workflows.

A workflow process comprises multiple steps (no limit aside from practicality), with multiple users assigned to the group responsible for providing approval at each step.

A user can be a member of multiple groups in the same workflow, although again, for practicality's sake, this probably doesn't make sense.

To initiate an approval workflow, a user updates content, saves their changes, then selects 'Request approval' from the editor button drawer.

After entering a comment describing the nature of the changes, and submitting the request, members of the approving group are notified via email, and have a task pushed into their workflow dashboard. When initiating a workflow, the user can choose to add a document attachment - eg an email approving or requesting the change be made.

Tasks can be approved (or cancelled or rejected) from the dashboard or from the content node button drawer.

The workflow dashboard updates to reflect the state of each task, providing an easy overview of a user's submissions and tasks.

Rejecting a task returns the workflow to the original author, who can update the content and resubmit. The resubmitted content does not restart the workflow, but returns to the stage at which it was rejected.

### Licensing

Plumber is a licensed product, but doesn't require a purchase to use. New installs are defaulted to a trial license, while the paid license is [available for purchase](https://thisredwolf.com). The trial license introduces some restrictions around advanced features but is otherwise a full-featured workflow platform. The paid license is valid for one top-level domain and all its subdomains.

To impersonate the full license on a local site, drop a test license (an empty text file named `test.lic`) into /App_plugins/Plumber. The test license is restricted to naked `localhost` or `localtest` domains, or domains ending in `.test` or `.local`.

### Settings

Plumber comes pre-wired with sensible defaults, but these should be modified to best suit your site.

- **Flow type:** how should the approval flow progress? These options manage how the change author is included in the workflow:
    - **Explicit:** all steps of the workflow must be completed, and all users will be notified of tasks (including the change author)
    - **Implicit:** all steps where the original change author is NOT a member of the group must be completed. Steps where the original change author is a member of the approving group will be completed automatically, and noted in the workflow history as not required.
    - **Exclude:** similar to Explicit, in that all steps must be completed, but the original change author is not included in notifications or shown dashboard tasks
- **Lock active content:** how should content in a workflow be managed? Set true or false to determine whether the approval group responsible for the active workflow step can make modifications to the content.
- **Exclude nodes:** nodes selected here are excluded from the workflow engine and will be published per the configured Umbraco user permissions. Requires license.
- **Document-type approvals:** configure workflows to be applied to all content of the selected document type. Refer to [Approval flow types](#approval-flow-types) for more information. Requires license.

### Notifications and reminders

Plumber 1.6.0 introduces a more mature and versatile notifications engine, allowing deep configuration of email notifications for all workflow activities. 

From the settings view in the Workflow backoffice section, the notifications dashboard provides access to the following:

- **Send notifications:** if your users are active in the backoffice, email notifications might not be required. Turn them off here.
- **Reminder delay:** set a delay in days for sending reminder emails for outstanding workflow processes. Set to 0 to disable. 
- **Workflow email:** set a sender address for notification emails. MANDATORY
- **Site URL:** the URL for the public website (including schema - http[s]). MANDATORY
- **Edit site URL:** the URL for the editing environment (including schema - http[s]). MANDATORY
- **Email templates:** configure which users receive emails for which workflow actions, and modify the templates for those emails

#### Notifications

Notification emails use HTML templates, rendering information from the `HtmlEmailModel` type, which lives in the `Plumber.Core.Models.Email` namespace. While it's entirely possible to modify the email templates from the backoffice, it's recommended to make changes via your IDE of choice, to enjoy the helping hand that is Intellisense.

The `HtmlEmailModel` looks like this:

- {WorkflowType} WorkflowType: an enum value, either 1 or 2, for Publish and Unpublish respectively
- {DateTime?} ScheduledDate: if a scheduled date exists for the workflow, it is found here
- {IHtmlString} Summary: a pre-generated representation of the current workflow state (essentially the same data as found in the pre-v1.6.0 email content
- {WorkflowTaskViewModel} CurrentTask: the view model data for the current workflow task. Comes with a whole lot of useful data, best explored via Intellisense
- {WorkflowInstanceViewModel} Instance: the view model data for the current workflow. Also best explored via Intellisense

Base fields from `HtmlEmailBase`:
- {string} SiteUrl: the public URL for your site
- {string} NodeName: the name of the node from the current workflow
- {string} Type: the UI-friendly workflow type. Includes the scheduled date if one exists
- {EmailType} EmailType: an enum value representing the current email type, which relates directly to the workflow task type
- {EmailUserModel} To: the model defining who receives the email
  - {string} Email: the email address (or group address if the group email is set)
  - {string} Name: the user's name
  - {string} Language: the user's language
  - {int} Id: the user's ID (or group ID when sending to a group email address) 	 	
  - {bool} IsGroupEmail: are we sending to a generic group email address?

Plumber provides settings for determining who receives emails at which stages of a workflow. While these are set to default values on install, it's advised that these are updated to better suit your install. Emails can be sent to:
- All: all participants in all workflow stages (previous and current)
- Group: all members of the group assigned to the current task
- Author: the user who initiated the workflow
- Admin: the admin user

By default, all emails are set to send to Group, but this isn't always the ideal - cancelled workflows would be best sent to the Author only, likewise with rejected. All would likely be most useful for notifying of completed workflows, but even this may be excessive. As said above, the best configuration will depend on your site, but most likely won't be the defaults.

#### Reminders

v1.6.0 introduces a reminder email system, to prompt editors to complete pending workflows. Reminders are sent using Umbraco's internal task scheduler, every 24 hours after an initial delay. For example, setting the reminder delay value to 2 will allow pending workflows to sit for 2 days before sending reminder emails every 24 hours to all members of the group assigned to the pending workflow task.

The emails use a similar model to the notification emails, also inheriting from `HtmlEmailBase`. In addition to the inherited fields, `HtmlReminderEmailModel` includes:
- {IList<WorkflowTaskViewModel>}: OverdueTasks: a list containing all the overdue tasks assigned to the current user
- {int} TaskCount: the count of overdue tasks assigned to he current user

#### Localization

All email models are fully localized (where translations exist), with the remaining template text available to edit in the backoffice or your IDE. Out of the box, Plumber's email templates are assumed to be for the default language. To add templates for other languages, duplicate the required template and append the culture code to the file name, prefixed with an underscorce:

- Default approval request template: `~/View/Partials/WorkflowEmails/ApprovalRequest.cshtml`
- Danish approval request template: `~/Views/Partials/WorkflowEmails/ApprovalRequest_da-DK.cshtml`

### Upgrades

Plumber will display a prompt on the Workflow section dashboard when a new version is available.

Displaying the prompt is determined by comparing the assembly version of the current installed version with the latest released version tagged in the GitHub repository.

If the remote version is newer than the installed package, the upgrade prompt displays the release notes and a link to the GitHub release. Updates should be installed via NuGet.

### Approval groups

Plumber uses a separate groups model from the rest of your Umbraco website. It's different, but looks familiar.

Add users to approval groups to determine which users will be responsible for approving content changes.

The Approval groups view in the Workflow section lists the active groups, their membership, permissions, and a quick link to email the group. Select a group from the list to edit settings, permissions and membership.

Total groups is limited to 5 on unlicensed installs, the paid license removes this restriction.

#### Settings
- **Group email:** sometimes it's more appropriate to send workflow notifications to a generic inbox rather than the individual group members. Add a value here to do exactly that.
- **Description:** it isn't used anywhere other than the group view. It's a note to remind you why the group exists.
- **Offline approval:** allow this group to approve changes without logging in to the Backoffice. Refer to [Offline approval](#offline-approval) for more information.

#### Roles
Provides an overview of the current workflow roles for the group, both node-based and document-type based.

#### Members
Manage the membership for the given user group. Members can be explicitly added to the group, or inherited from existing Umbraco groups. Ideally group members are set explicitly to ensure changes made to Umbraco groups don't cause unexpected changes to workflow permissions.

#### History
Provides an overview of the workflow activity for the current group.

### Approval flow types<a name="approval-flow-types"></a>

Approval flows come in three flavours: explicit, inherited and document-type.

A given content node may have all three approval flow types applied, but only one will be applied per the following order of priority:

- **Explicit:** set directly on a content node via the context menu. This type will take priority over all others.
- **Document-type:** set in the settings section. This approval flow will apply to all content of the selected document type, unless the node has an explicit flow set. Requires  license.
- **Inherited:** if a node has no explicit approval flow, nor a flow applied to its document-type, Plumber will traverse the content tree until it finds a node with an explicit flow, and will use this flow for the current change.

Current responsibilites for groups can be reviewed on the user group view, for explicit and document-type approval flows only.

Document-type approval flows can also include conditional groups - ie only include Group B in the workflow when the meta-description property has changed.

### Dashboards and buttons

Plumber adds two dashboards to the Umbraco install:

- **User dashboard:** added in the content section, this view displays all submissions and pending tasks for the current user
- **Admin dashboard:** the default view in the Workflow section, displaying a chart of recent workflow activity, and any relevant messaging related to upgrades

Plumber replaces the default Umbraco button set in the editor drawer. When a workflow is active on the current node, the button is singular, linking to the workflow content app. When no workflow is active, the button state is determined by the current user's permissions.

Plumber overrides Umbraco's user/group publishing permissions. Provided the user has permission to update the node, they will be able to intiate a workflow process on that node. Plumber essentially shifts Umbraco from a centrally administered publishing model (ie controlled by a site administrator) to a distributed model, where editors publish content based on their responsibilities assigned through inclusion in workflows.

In cases where the content is already in a workflow, a notification is displayed at the top of the editor (depending on settings, content edits may also be disabled). For nodes where the workflow has been disabled, the default Umbraco options are displayed.

### Content app

Plumber adds a content app to all nodes where workflow is enabled. The Workflow content app includes three sub-sections: Active workflow, Configuration and History

#### Active workflow
The Active workflow sub-section provides the interface for managing workflows for the current content node - from this view, content can be submitted for workflow approval and pending workflow tasks can be rejected, cancelled or approved.

In multi-lingual sites, variant content can be submitted into one of three different workflows. Either submmit only the current variant, submit all variants for publishing in a single workflow process using the workflow applied to the default variant, or submit each variant into a separate workflow - eg, have the German version of your content approved by groups of German speakers, and the English version by English speakers.

When the current node is pending workflow approval, the Active workflow sub-section displays more detailed information - the requesting user, their comments, the pending variant(s), and any workflow activity (eg task approvals/rejects) for the current workflow process.

Users must provide a comment before actioning a task.

Workflow administrators (those users with access to the Workflow section) can action workflows assigned to a different group. In the workflow history, these are noted as being performed by admin.

#### Configuration
The Configuration sub-section provides the interface for configuring (surprise!) the approval flow for the current node, and displays any inherited or content-type approvals applied to the current node. Adding an approval flow is as easy as selecting groups from the dropdown, and clicking Add group. 

Approval flow groups can be reordered via drag and drop. 

Configuration can not be modified when a content node is in a workflow process.

#### History
The History sub-section provides a chronological audit trail of workflow activity for the current node. The Detail button in the right-most column launches an overlay with similar content to that displayed on the Active workflow sub-section.

### Offline approval

Groups can optional be given permission to action workflow tasks without logging in to Umbraco. This feature requires a paid license.

By setting the Offline Approval checkbox to true on the edit group view, all email notifications sent to members of the group will include a personalized link to a preview page.

The preview page exposes the current saved page, with the options to approve or reject the change. It is not possible to edit the content from the offline approval view.

This feature is intended for use in situations where the approval group membership is a single user who would not otherwise be using Umbraco - for example, a manager may want to approve media releases before publishing, but does not othewise need access to Umbraco.

Offline approval does require a user exist in the backoffice, and be assigned to a workflow group - just like any other workflow participant - but they need not know how to use Umbraco, or even know their login credentials.

### Events

Plumber raises events in a similar fashion to Umbraco - if you're familiar with Umbraco's events, Plumber won't have any surprises.

Currently, events are raised by the Config, Group and Tasks services, and the DocumentPublish and DocumentUnpublish processes and can be subscribed to as follows:

Events are not cancellable, and serve to provide an entry point for writing custom notification layers - Slack, SMS, whatever you choose. 

#### ConfigService

The Config service is responsible for managing workflow configuration for nodes and content types.

Events are raised whenever a node or content type configuration is updated.

#### GroupService

The Group service is responsible for managing approval groups.

This service raises events whenever an approval group is created, updated or deleted.

#### TasksService

The Tasks service is responsible for all operations involving workflow tasks.

This service raises events whenever a task is created or updated.

#### DocumentPublishProcess and DocumentUnpublishProcess

These processes are the core of the workflow, and manage instance/task creation and workflow progression.

The processes raise events whenever a workflow instance is created or updated.

#### Event subscription

Subscribe to events in the same way as Umbraco events - by adding a handler in a Component:

```csharp
public class ContentEventsComponent : IComponent
{
	public void Initialize()
	{
		GroupService.Updated += GroupService_Updated;
	}

	private void GroupService_Updated(object sender, GroupEventArgs e) {
		// do stuff whenever a group is updated
	}
}
```

For all services, `e` will provide the object being created, updated or deleted. 
