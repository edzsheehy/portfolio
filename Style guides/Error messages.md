# Writing sample - error message style guide

> I wrote this internal style guide to set a consistent standard for writing error messages across all product interfaces.

# Guidelines by component - error messages

When something goes wrong with our products, the language we use to convey the message to our users should be direct, concise, conciliatory, and if possible, actionable. This applies to both user-attributed errors and program failures. These guidelines will help you write suitable error messages for a variety of scenarios.

## What constitutes an error?

The word "error" in the context of an application specifically means:

* Something went wrong with the application unexpectedly.
* The current conditions set by the user prevent the application from advancing a workflow or completing a task.

For an in-product notification to call itself an error, it must meet one of these criteria.

### Program errors

Sometimes the application just fails. The user isn't at fault here. The following are typical program errors:

* Thrown exceptions
  * When handled properly, the application can recover gracefully.
  * When unhandled, the application can crash completely.
* Loading failures
* Save failures
* Connectivity failures

### User errors

Sometimes the user is responsible for the application getting stuck. The following are typical user-attributed errors:

* Invalid input
  * The user populates fields in the wrong format, uses forbidden characters, etc.
* Invalid configuration
  * The user configures something with conflicting criteria.
* Unmet requirements
  * The user leaves required fields blank, does not have the correct permissions for the action, etc.

### Non-errors

The following scenarios are not errors, but are sometimes misattributed as such. Keep an eye out for cases like these so you can categorize them correctly for the user:

* A program or component is inactive
  * For programs or components that are designed to perform jobs or ingest specific events, inactivity is not necessarily a sign that something went wrong. There simply may not be any events to process. This should be reported as a **warning**, not an error.
  * The exception to this is when the program or component's function is supposed to be observably constant. Inactivity like this should be reported as an **error**.
* A component is near resource exhaustion
  * It's common to advise a user when a component is approaching the limits of its hardware, which can eventually impact the performance of the application. Nothing has gone wrong yet, so this should be reported as a **warning**, not an error.
  * However, an application that crashes because the component has exceeded the limits of its resources should be reported as an **error**.
* A program displays no data
  * Data visualization products often allow users to filter a data set for specific records. As long as the user provided a valid query, the return of no results by the program is not an error; it just means no records match the criteria. This scenario is known as an **empty state** or **null state** and any language used to explain it should be reported as a **notice**.
  * However, queries can also have invalid formatting that prevents the application from executing a search. This is indeed an **error**.

## General guidelines

All error messages should follow these rules:

* **Use a professional tone** - Don't infantilize the user with words and phrases like "oops!" and "uh oh!". Use a professional tone that reflects the user's expectation that we take them seriously.
* **Keep the message short** - Above all, error messages must be consumable. While the explanation for an error may be complex and esoteric, the first report of the error in the product interface is not the context for that type of language. State the issue plainly so the user has a basic understanding of what happened.
  * Fragment sentences are acceptable for error messages if a complete sentence is unnecessarily wordy.
* **If the error subject has a user-configured name, use that name in the message** - Entities like cards, dashboards, reports, and workflows are often named by the user when they are created (for example, a dashboard called **Los Angeles Linux servers**). When something goes wrong with one of these entities, include the user-configured name in the message as bolded text (unless the interface context makes stating it redundant).
  * If the error subject does not have a user-configured name, make sure its general name is understandable.
* **Don't blame the user with "you" or "your"** - Writing in the second person is fine in most cases, but avoid doing so in the context of reporting errors. Error messages describe failures, not successes. Users may interpret second person language like "you" or "your" as the application blaming them for the failure. Use "the", "this", "a", or "an" instead.
  * "This" is useful when you need to report an error for something in a spatial way in relation to the rest of the interface. For example, in a dashboard with several cards, one card fails to load while the rest load successfully. In this case, "**This** card failed to load" makes more sense visually than "**The** card failed to load."
  * "A" or "an" are useful when you need to report an error where the subject does not have primacy compared to other subjects. For example, for a component that performs several collection jobs on a repeating basis, one of those jobs failing while the others complete successfully can be reported as "**A** collection task failed."
* **All error messages must be sanitized** - This means the initial error report must be in a human-readable format. Tracebacks, exception output, codes, and similar error text should not be the **primary** content that is displayed.
  * Error details like these still have their use (especially for troubleshooting by the Support team), so feel free to make them available if the user explicitly wants to seem them through a call-to-action.
* **Only provide next steps if the user can perform them quickly and success is likely** - When an error occurs, don't burden the user with complicated troubleshooting steps (this is especially true if the error is not user-attributed) unless the solution is purpose-built to solve the problem and is documented elsewhere. The interface is not the right context for this and only frustrates the user. Further, if the user can take an action to solve the problem, only suggest it if there's a good chance it will work.
  * If you need to provide next steps, skip the formality of saying "please" and just tell the user directly what they need to do.
* **Avoid meaningless suggestions** - Nonspecific suggestions such as "Try again later" are not valuable and read as dismissive. If a suggestion is warranted, it should also be actionable right away.
* **Recommend contacting the Support team when the situation calls for it** - Some errors can't be self-serviced and are significant enough that Support should be involved. When an error meets this criteria, the error message should include a direct link to creating a case with the Support team.

## Error message template

All error messages should follow this structure:

`{Error subject}` `{failing verb phrase}` `{receiving or affected object, if necessary}` `{error reasoning, if necessary}`. `{Next steps to take, if necessary}`.

1. `{Error subject}` - The error subject is the application, process, action, entity, etc that experienced the failure.
   * If stating the error subject is redundant in the context of an interface, you can omit it.
2. `{failing verb phrase}` - The verb phrase that follows the error subject states what happened.
3. `{receiving or affected object, if necessary}` - If the error subject failed to do something to another entity, state that entity here.
4. `{error reasoning, if necessary}` - If the reason for the failure is known, state the reason here.
5. `{Next steps to take, if necessary}` - If providing next steps is suitable according to the [general guidelines](#general-guidelines), state those next steps as a new sentence after the initial error message.

## Examples

These example error messages cover a variety of both user-attributed and program error scenarios.

### Generic messaging

In cases where errors are unforeseen and no sanitization exists for them, use this generic error message:

_Something went wrong._

### Process failures

_A collection task terminated unexpectedly._

_The scan stopped due to host memory limits._

_The requested resource does not exist._

_Data collection stopped due to excessive invalid requests. Restart the device._

_The console failed to restore with the selected backup. Contact Support._

### Loading failure

_This page failed to load. Refresh your browser tab._

### Creation, generation, or deletion failures

These examples include the user-assigned name for the error subject:

_The **April risk trend** report failed to generate._

_The **Security team Jira tickets** connection could not be deleted._

### Save failures

_Your changes to the **Patch Tuesday** dashboard could not be saved because another user is inspecting it._

### Connectivity failures

_The sensor failed to detect any network traffic._

_The console didn't respond._

_The scan failed due to network connectivity loss._

_The browser session expired. Refresh your browser tab._

### Field validation failures

In general, print `Required` next to required fields that the user has left blank if they attempt to navigate away from or advance a workflow without filling them out.

This example explains character restrictions to the user:

_Input can only consist of upper and lowercase letters (A-Z, a-z), numbers (0-9), and hyphens (-)._

Other specialized examples:

_This username already exists._

_This email address is already associated with another account._

_Email addresses must be in a valid format: example@domain.com_

### Permission restrictions

_This user account does not have permission to generate reports. Contact your administrator to request access._

_This user account is suspended. Contact your administrator._
