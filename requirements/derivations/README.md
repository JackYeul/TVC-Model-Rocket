# Derivation documents

Supporting material that justifies a requirement: hand calculations, trade
studies, sizing spreadsheets, test reports, vendor datasheets. The requirement
text says *what* is required; the document here says *why* that number.

## Naming

Name each file after the requirement it justifies, so the pairing survives a
reorganization:

    <ITEM_ID>_<short_topic>.<ext>      e.g. SUB001_latency_budget.pdf

If one document justifies several requirements, name it after the topic and
reference it from each item.

## Three fields, three jobs

`rationale:` is plain prose, stored on the item itself: why this number, what
drove the choice, what was rejected. It stands alone, so a reader needs no
attachment to follow the reasoning. Keep it to a **single-line** YAML string --
a multi-line block scalar breaks the attribute table in the published output.
Use `<br>` where a line break is wanted:

```yaml
rationale: '50 Hz floor: rigid-body pitch mode is near 3 Hz, ten samples per period.<br>20 ms cap: at 3 Hz that costs ~22 deg of phase margin.'
```

`references:` and `derivation:` point at the supporting document. They do two
different jobs, so use both.

`references:` is checked by Doorstop. Validation fails if the file is moved,
renamed or deleted, so an attachment cannot silently rot. The path is relative
to the repository root, and `type: file` means the whole file is the reference
(no keyword search), which is what binary files such as PDFs need:

```yaml
references:
- path: requirements/derivations/SUB001_latency_budget.pdf
  type: file
```

`derivation:` is what a reader clicks. Doorstop renders the published
`references:` entry as plain text, but custom attributes render Markdown, so
this one becomes a real link in the published HTML. Use the GitHub blob URL,
not a relative path: relative paths resolve from the published output
directory and break whenever the output moves.

```yaml
derivation: '[Latency budget hand calc](https://github.com/JackYeul/TVC-Model-Rocket/blob/main/requirements/derivations/SUB001_latency_budget.pdf)'
```

`derivation` is declared in every document's `.doorstop.yml`, so items created
by `doorstop add` start with it empty and items that predate it simply omit the
row when published. Adding one to an existing item is a normal edit: it
invalidates that item's `reviewed:` fingerprint and marks its children's links
suspect, so re-read the affected items, then `doorstop review <ID>` and
`doorstop clear <CHILD_ID>`.

## Formats

Export handwritten work as PDF rather than an app-specific format. `*.pdf` is
already marked binary in `.gitattributes`. If scans grow to many megabytes,
move them to Git LFS before the repository bloats.
