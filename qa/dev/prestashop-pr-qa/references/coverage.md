# What to check

Checklist to run over a scenario once the ticket's steps are written. Pick the sections the diff touches, skip the rest, and name the skipped ones in the report.

Bold items are the ones that have already been missed. Append to this file when a new miss is found.

## Form

### Each input, on create and on update

- set a value, save, reopen: it is there
- leave an optional input empty, save: no crash, stored empty
- change the value, save, reopen: it changed
- **clear the value, save, reopen: it is actually empty** (not silently kept)
- **open the form and save without touching anything: nothing changed** (run this on a record with awkward values, not one you just created through this form)
- clear a required input: a field-level error, not a generic flash and not a 500
- omit the input from the payload entirely, rather than blanking it: same
- a value at the column's max length, and one over it
- a value the merchant's locale produces: `48,8566` for a decimal, accents, leading and trailing spaces
- a value storage can hold but the widget cannot render: three slots in a two-slot widget
- `0` and a negative number, where the input is numeric
- the same edit twice in a row

Read the result from the database, not from the flash message. A save that changed nothing still says *Successful update*.

### The form as a whole

- **field order matches the page being replaced**
- inputs the old page grouped on one row are still grouped
- required inputs are the same set as before
- default values on the create form (preselected country, active switch)
- translatable inputs: fill one language only, save, check the other is untouched
- upload an image, then replace it, then delete the record

## Data already in the database

The demo data is the shape the new code was written for, so it cannot fail. Add a fixture holding:

- **more elements than the new widget renders** (a legacy free-form field)
- the old serialisation, where both shapes are valid on disk at once
- `NULL` where the form always writes `''`, and the reverse
- a value written by an older major version
- an orphan row whose parent is gone
- a record belonging to another shop, or to none
- a translatable field present in one language only

Apply the fixture identically before both phases, and list it in the report with its SQL.

## Grid and listing

- every column shows the value the record actually has
- filter, sort and reset on each column
- filters survive a page reload
- row actions: edit, delete, toggle status
- bulk actions on a selection, and on none
- delete leaves no orphan rows behind

## Clients that are not a browser

The browser enforces `required`, `maxlength` and the form's JavaScript. The Admin API and modules do not.

- **post with a required field removed from the body: a validation error, not a 500**
- post the raw value the merchant typed, skipping the JavaScript that normalises it
- post an id that is not in the select

Post through the page's own request context, not by navigating: a navigation answering 500 is recorded as a failed precondition and voids the run.

## Migration parity

- what the legacy controller did after saving: normalisation, cache invalidation, image loop
- every `Hook::exec()` the legacy controller fired
- the wording of each validation message
- what legacy rejected and the new page accepts, and the reverse (the reverse is sometimes an improvement, worth one line in the report)

## Errors and messages

- **every exception code the domain throws has a message mapped**
- **every mapped code is actually thrown somewhere** (a mapped code never thrown usually means the real one was forgotten)
- a validation failure points at the field, not only at the top of the page

## Images

- every configured generation format is produced, not only JPG
- the theme template's `<source>` elements resolve
- replacing an image removes the old derivatives
- a file that is not an image, and one over the upload limit

## Configuration keys

- the value shape each key holds is unchanged (an ISO code where a label used to be)
- grep who reads the key across `classes/ controllers/ src/ modules/ themes/ mails/`
- a key the old page wrote and the new one does not

## Multistore

- the shop association is stored and honoured on read
- a record in shop A is invisible from shop B
- editing from an "all shops" context does not reassign the record
- the association survives an edit that never touches that field

## The PR's own tests

- **does an assertion helper silently ignore fields absent from its table?** An `if (isset($data[...]))` chain passes without asserting anything
- **do the fixture values exercise the branch the PR fixed?** A coordinate fix tested only with decimals leaves the whole-number path untested
- does any existing campaign reach the changed field at all? Equal pass counts prove only that the covered behaviour did not move
- one test per fix in the branch's history

## Quick greps

Pass `-F` when the pattern contains `$`, and point `-r` at a directory: a `**` glob matches one level only unless `globstar` is on, and it is off by default.

A field in both lists cannot be cleared through the form:

```bash
DOMAIN=Store
grep -rnE "\\\$data\['[a-z_0-9]+'\] \?: null" "src/Core/Form/IdentifiableObject/DataHandler/${DOMAIN}FormDataHandler.php"
grep -rn -F 'if (null !== $command->get' "src/Adapter/${DOMAIN}/CommandHandler/"
```

A field first added after the main builder chain renders last:

```bash
grep -rnE '(\$builder->add|\$this->rebuild[A-Za-z]+)\(' src/PrestaShopBundle/Form/Admin/
```

Exception codes, thrown against mapped:

```bash
grep -rn -F "${DOMAIN}ConstraintException::" src/Adapter/ src/Core/
grep -rn -F "${DOMAIN}ConstraintException::" src/PrestaShopBundle/Controller/
```

`handleRequest()` outside the `try` answers 500 on any constraint violation, because Symfony validates on `POST_SUBMIT`:

```bash
grep -rn -F -B2 -A8 'handleRequest($request)' "src/PrestaShopBundle/Controller/Admin/Configure/ShopParameters/${DOMAIN}Controller.php"
```

## Where the bold items come from

All from PrestaShop/PrestaShop#41414, which two QA passes reported green before a third found them: phone, fax and email could not be cleared; a day with four hour slots was wiped on a plain save; State rendered last instead of next to Country; an empty country answered 500; one exception code was thrown but unmapped and another mapped but never thrown; and the two Playwright campaigns passed on both sides of the diff without reaching either broken field.
