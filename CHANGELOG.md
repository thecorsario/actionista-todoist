


2019-09: Move to Todoist Sync API v8
-------------------------------------

### What has changed between v7 and v8 of the Sync API?

Task `due` property changes:

* Task items not have a dedicated `due` dict property, instead of the `due_date_utc` and friends.
	* This change, with a dedicated `due` property, was actually already present in the Sync API v7.1.
* This affect these methods:
	* The `items.add()` and `items.update()` methods now expect a `due` parameter,
	  instead of the `date_string`, `date_lang` and/or `due_date_utc` parameters.
	* The `items.update_date_complete()` method now expects a `due` parameter,
	  instead of `new_date_utc`, `date_string` and/or `is_forward` parameters.
* The new `due` property has the following:
	* `date` (str): RFC-3339 date, either "YYYY-MM-DD" or "YYYY-MM-DDTHH:MM:SS".
		If `date` is YYYY-MM-DD, it is considered an "all day" item with no specified time.
		This is different from the v7 API, where a time of 23:59:59 was used to indicate an "all day" task.
	* So, a major difference is that an "all-day" task in v7 API had a time of 23:59:59,
		while an "all day" task in v8 API does not have a time.
	* `timezone` (str): Always set to null, unless you want the task due time to really be at a given timezone.
		If timezone is not given, then the time is always "in the user's current timezone".
		That is, if I specify a task to happen at 10:00:00, and I change timezone, the task is still due
		at 10:00:00 in the new timezone.
	* `string` (str): A human-readable due date, e.g. "10 am every monday".
	* `lang` (str): The language used to parse the `string` attribute.
	* `is_recurring` (bool): Whether the task is recurring. If it is, then completing a task
		will simply move the task's due date by re-parsing the `string`.

Reminders:

* The `date_string`, `date_lang`, `due_date_utc` properties of reminders were
  replaced by the `due` object.
* The `reminders.add()` and `reminders.update()` methods now expect a `due`
  parameter, instead of the `date_string`, `date_lang` and/or `due_date_utc`
  parameters.

Date format changes:

* Date formats must be RFC-3339, i.e. "YYYY-MM-DD" or "YYYY-MM-DDTHH:MM:SS" or (maybe?) "YYYY-MM-DD HH:MM:SS"
	* From the RFC-3339 specs: [RFC-3339 is] "a profile of the ISO 8601 standard".

Other method changes:

* Most of the `items` methods are now intended to be invoked on just *a single*
	item, rather than a list of items.
* For example, the `items.complete()` method now expects the `id` parameter, instead of
  the `ids` parameter, and it completes the item and all the item's
  descendants.  In addition the new `date_completed` parameter can also be
  specified.


Refs:

* https://developer.todoist.com/sync/v8/#changes
* https://developer.todoist.com/sync/v8/#full-day-dates
* https://github.com/Doist/todoist-python/blob/29864f6b390f64b560fad891fdb9d3c26ca5237f/CHANGELOG.md
* https://github.com/Doist/todoist-python/commit/29864f6b390f64b560fad891fdb9d3c26ca5237f


Copy/paste of official Todoist CHANGELOG.md:

* All arguments expecting a date/time must be formatted according to [RFC
  3339](https://tools.ietf.org/html/rfc3339), and all return values are also
  using the same format.
* The `item_order` and `indent` properties of projects, that denoted a visual
  hierarchy for the projects (the order of all the projects and the level of
  indent of each one of them), were replaced by `parent_id` and `child_order`,
  which denote a real hierarchy (the parent project of a project and the order
  of all children of a specific parent project).
* The `projects.add()` method now expects a `parent_id` and `child_order`
  parameter, instead of the `item_order` and `indent` parameters.
* The `projects.update()` method doesn't expect an `item_order` and `indent`
  parameters anymore, but it doesn't accept the new `parent_id` and
  `child_order` parameters as well, as the way to change the hierarchy is now
  different (see the `projects.move()` and `projects.reorder()` methods).
* The new `projects.move()` method must be used to move a project to become
  the child of another project or become a root project.
* The new `projects.reorder()` method must be used to reorder projects in
  relation to their siblings with the same parent.
* The `projects.delete()` method now expects only an `id` parameter, instead
  of the `ids` parameter, and it deletes the project and all the projects's
  descendants.
* The `projects.archive()` method now expects the `id` parameter, instead of
  the `ids` parameter, and it archives the project and all the project's
  descendants.
* The `projects.uncomplete()` method now expects an `id` parameter, instead
  of the `ids` parameter, and it restores the project as a root project.
* The `projects.update_orders_indents()` method was removed.
* The `date_string`, `date_lang`, `due_date_utc` properties of items were
  replaced by the `due` object.
* The `item_order` and `indent` properties of items, that denoted a visual
  hierarchy for the items (the order of all the items and the level of indent
  of each one of them), were replaced by `parent_id` and `child_order`, which
  denote a real hierarchy (the parent item of an item and the order of all
  children of a specific parent item).
* The `items.add()` method now expects a `parent_id` and `child_order`
  parameter, instead of the `item_order` and `indent` parameters.
* The `items.add()` and `items.update()` methods now expect a `due` parameter,
  instead of the `date_string`, `date_lang` and/or `due_date_utc` parameters.
* The `items.update()` method doesn't expect an `item_order` and `indent`
  parameters anymore, but it doesn't accept the new `parent_id` and
  `child_order` parameters as well, as the way to change the hierarchy is now
  different (see `item_move` and `item_reorder`).
* The `items.move()` method does not accept the `project_items` and
  `to_project` parameters, but a new set of parameters specifically `id`, and
  one of `project_id` or `parent_id`.  Another difference stemming from this is
  that only a single item can be moved at a time, and also that in order to
  move an item to become the child of another parent (or become a root level
  item) the `item_move` command must be used as well.
* The `items.update_orders_indents()` method was removed.
* The new `items.reorder()` method must be used to reorder items in relation
  to their siblings with the same parent.
* The `items.delete` method now expects only an `id` parameter, instead of
  the `ids` parameter, and it deletes the item and all the item's descendants.
* The `items.complete()` method now expects the `id` parameter, instead of
  the `ids` parameter, and it completes the item and all the item's
  descendants.  In addition the new `date_completed` parameter can also be
  specified.
* The `items.uncomplete()` method now expects an `id` parameter, instead of
  the `ids` parameter, and it uncompletes all the item's ancestors.
* The new `items.archive()` method can be used to move an item to history.
* The new `items.unarchive()` method can be used to move an item out of
  history.
* The `items.update_date_complete()` method now expects a `due` parameter,
  instead of `new_date_utc`, `date_string` and/or `is_forward` parameters.
* The possible color values of filters changed from `0-12` to `30-49`.
* The `date_string`, `date_lang`, `due_date_utc` properties of reminders were
  replaced by the `due` object.
* The `reminders.add()` and `reminders.update()` methods now expect a `due`
  parameter, instead of the `date_string`, `date_lang` and/or `due_date_utc`
  parameters.
* The state now includes an additional new resource type called
  `user_settings`.
* The user object now includes the `days_off` property.
* The `since` and `until` parameters of the `activity/get` method are
  deprecated, and are replaced by the new `page` parameter.