### Overview

Plumber extends Umbraco's standard publishing model to allow creation of multi-stage approval workflows.

A workflow process can comprise multiple steps (no limit aside from practicality), with multiple users assigned to the group responsible for providing approval at each step.

A user can be a member of multiple groups in the same workflow, although again, for practicality's sake, this probably doesn't make sense.

To initiate an approval workflow, a user updates content, saves their changes, then selects 'Request approval' from the editor button drawer.

After entering a comment describing the nature off the changes, and submitting the request, members of the approving group are notified via email, and have a task pushed into their workflow dashboard.

Tasks can be approved (or cancelled or rejected) from the dashboard or from the content node button drawer.

The workflow dashboard updates to reflect the state of each task, providing an easy overview of a user's submissions and tasks.

Rejecting a task returns the workflow to the original author, who can update the content and resubmit. The resubmitted content does not restart the workflow, but returns to the stage at which it was rejected.

### Licensing

Plumber is a licensed product, but doesn't require a purchase to use. Licenses are offered across three tiers:
- **Trial:** the Trial license is free, limits total groups to 5 and restricts access to some advanced features (content-type approval flows, excluding nodes, offline approval)
- **Basic:** enables all features but maintains a 10-group limit
- **Pro:** unlimited everything

All licenses are tied to a single production domain and any subdomains, plus localhost on any port. 

The licensing model uses Stripe as a payment provider, and requires a periodic network request to check for a valid license (ie Plumber will poll the payment server when the local license has expired, requesting the refreshed license). If this is not viable for your install, the one-off lifetime license removes this requirement.

Licenses can be purchased directly from the backoffice, from the Licensing view in the Workflow section.

### Settings

Plumber comes pre-wired with sensible defaults, but these should be modified to best suit your site.

- **Flow type:** how should the approval flow progress? These options manage how the change author is included in the workflow:
    - **Explicit:** all steps of the workflow must be completed, and all users will be notified of tasks (including the change author)
    - **Implicit:** all steps where the original change author is NOT a member of the group must be completed. Steps where the original change author is a member of the approving group will be completed automatically, and noted in the workflow history as not required.
	- **Exclude:** similar to Explicit, in that all steps must be completed, but the original change author is not included in notifications or shown dashboard tasks
- **Lock active content:** how should content in a workflow be managed? Set true or false to determine whether the approval group responsible for the active workflow step can make modifications to the content.
- **Send notifications:** if your users are active in the backoffice, email notifications might not be required. Turn them off here.
- **Workflow email:** Set a sender address for notification emails. This defaults to the system email as defined in umbracoSettings.config
- **Site URL:** the URL for the public website (including schema - http[s])
- **Edit site URL:** the URL for the editing environment (including schema - http[s])
- **Exclude nodes:** nodes selected here are excluded from the workflow engine and will be published per the configured Umbraco user permissions. Requires Basic or Pro license.
- **Document-type approvals:** configure workflows to be applied to all content of the selected document type. Refer to [Approval flow types](#approval-flow-types) for more information. Requires Basic or Pro license.

### Upgrades

Plumber will display a prompt on the Workflow section dashboard when a new version is available.

Displaying the prompt is determined by comparing the assembly version of the current installed version with the latest released version tagged in the GitHub repository.

If the remote version is newer than the installed package, the upgrade prompt displays the release notes and a link to the GitHub release. Updates should be installed via Nuget.

### Approval groups

Plumber uses a separate groups model from the rest of your Umbraco website. It's different, but looks familiar.

Add users to approval groups to determine which users will be responsible for approving content changes.

The Approval groups view in the Workflow section lists the active groups, their membership, permissions, and a quick link to email the group. Select a group from the list to edit settings, permissions and membership.

Total groups is limited to 5 on the Trial license and 10 on Basic. The Pro-level license removes this restriction.

#### Settings
- **Group email:** sometimes it's more appropriate to send workflow notifications to a generic inbox rather than the individual group members. Add a value here to do exactly that.
- **Description:** it isn't used anywhere other than the group view. It's a note to remind you why the group exists.
- **Offline approval:** allow this group to approve changes without logging in to the Backoffice. Refer to [Offline approval](#offline-approval) for more information.

#### Roles
Provides an overview of the current workflow roles for the group, both node-based and document-type based.

#### Members
Manage the membership for the given user group. Easy.

#### History
Provides an overview of the workflow activity for the current group.

### Approval flow types<a name="approval-flow-types"></a>

Approval flows come in three flavours: explicit, inherited and document-type.

A given content node may have all three approval flow types applied, but only one will be applied per the following order of priority:

- **Explicit:** set directly on a content node via the context menu. This type will take priority over all others.
- **Document-type:** set in the settings section. This approval flow will apply to all content of the selected document type, unless the node has an explicit flow set. Requires Basic or Pro license.
- **Inherited:** if a node has no explicit approval flow, nor a flow applied to its document-type, Plumber will traverse the content tree until it finds a node with an explicit flow, and will use this flow for the current change.

Current responsibilites for groups can be reviewed on the user group view, for explicit and document-type approval flows only.

Document-type approval flows can also include conditional groups - ie only include Group B in the workflow when the meta-description property has changed.

### Dashboards and buttons

Plumber adds two dashboards to the Umbraco install:

- **User dashboard:** added in the content section, this view displays all submissions and pending tasks for the current user
- **Admin dashboard:** the default view in the Workflow section, displaying a chart of recent workflow activity, and any relevant messaging related to upgrades

Plumber replaces the default Umbraco button set in the editor drawer. Depending on user permissions, content state and workflow state, the button set will display one of the following:

- Save
- Request publish
- Approve changes

The button set dropdown will include additional options:

- Reject changes
- Cancel workflow
- Request unpublish

Plumber overrides Umbraco's user/group publishing permissions. Provided the user has permission to update the node, they will be able to intiate a workflow process on that node. Plumber essentially shifts Umbraco from a centrally administered publishing model (ie controlled by a site administrator) to a distributed model, where editors publish content based on their responsibilities assigned through inclusion in workflows.

In cases where the content is already in a workflow, a notification is displayed next to the button set. For nodes where the workflow has been disabled, the default Umbraco options are displayed.

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

While there's no mechanism preventing modifying the approval flow when content is in an active workflow, doing so may (probably will) have unexpected consequences. Best to close any active processes before modifying the approval flow.

#### History
The History sub-section provides a chronological audit trail of workflow activity for the current node. The Detail button in the right-most column launches an overlay with similar content to that displayed on the Active workflow sub-section.

### Offline approval - v1.1.0+

Groups can optional be given permission to action workflow tasks without logging in to Umbraco.

By setting the Offline Approval checkbox to true on the edit group view, all email notifications sent to members of the group will include a personalized link to a preview page.

The preview page exposes the current saved page, with the options to approve or reject the change. It is not possible to edit the content or cancel the workflow from the offline preview.

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

Subscribe to events in the same way you would any native Umbraco events - by adding a handler in a Component:

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

For all services, `e` will provide the object being created, updated or deleted (typically a poco). 

### Deployment tools<a name="deployment-tools"></a>

This feature is experimental. Use it at your own risk.

The Deployment tab in the Workflow section provides an interface for exporting and importing Plumber configuration, for deployment across different environments. 

The endpoints used for both actions can be access programmatically as part of a deployment script.

For manual exports, the Export Plumber config does exactly that - generates a JSON representation of all data in the Plumber database tables (settings, usergroups, user2usergroup and usergrouppermissions).

This JSON model can then be re-imported into other environments.

Warning - the import is destructive and can not be reversed. If the uploaded configuration model is valid, all data in the relevant tables will be deleted and overwritten.
