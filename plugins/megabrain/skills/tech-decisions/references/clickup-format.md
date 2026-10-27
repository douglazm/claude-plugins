# Writing the document into a ClickUp card

Read this before Step 6 pushes anything. Every rule here was learned against a real card.

## Whitespace

No blank line between a heading and the content under it. A heading and its first
paragraph, bullet or table are one unit — write them on consecutive lines. Blank lines
separate topics only: one decision from the next, a paragraph from the `Discarded` list, a
section from the following section. ClickUp keeps the heading and the content as separate
blocks without the blank line; the only place it reinserts one on its own is before a
table, which is a block embed and cannot be avoided.

## `Discarded` is a nested list, never a heading

The engineer converts these blocks into ClickUp toggle lists by hand, and a nested list is
one click away from a toggle while a heading is not. Always write `*   Discarded` with the
rejected options indented under it. Never inline them into the decision paragraph and never
demote them to plain bold text.

## Toggle lists cannot be written through the API

Tested against a real card: `<details><summary>`, a `:::toggle` fence, and a nested bullet
list all come back as literal text or a plain nested list. Worse, a toggle created in the UI
*exports* as a nested bullet list byte-identical to a hand-written one, so reading
`markdown_description` back cannot tell you whether a toggle survived — only the rendered
card can. Toggle exists in ClickUp's rich text as the Quill attribute `"list": "toggled"`,
but only the comments API accepts rich text; the task description takes markdown only.
Write the nested list and leave the toggling to the engineer.

## Bold next to inline code

ClickUp mangles a bold span that ends right before an inline code span —
`**chama** \`order.createBulk\`` comes back as two split bold runs. Keep the code span
outside the bold, or reword so bold and code don't touch.

## Appending, never overwriting

Read the current description first (`clickup_get_task` with `include: ["description"]`),
then write back the existing content, a `---` divider, and the new document. Nothing else
goes between them. Never post the document as a comment instead.
