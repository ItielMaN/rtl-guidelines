# RTL Guidelines

RTL languages such as Arabic, Hebrew, Persian and Urdu are read and written from right-to-left, and the user interface for these languages should be mirrored to ensure the content is easy to understand.

When a UI is changed from LTR to RTL (or vice-versa), it’s often called mirroring. An RTL layout is the mirror image of an LTR layout, and it affects layout, text, and graphics.

In RTL, anything that relates to time should be depicted as moving from right to left. For example, forward points to the left, and backwards points to the right.

### Mirroring layout

When a UI is mirrored, these changes occur:

* Text fields icons are displayed on the opposite side of a field
* Navigation buttons are displayed in reverse order
* Icons that communicate direction, like arrows, are mirrored
* Text is usually aligned to the right

Do | Don't do
-- | --------
`margin-inline-start: 5px;` | `margin-left: 5px;`
`padding-inline-end: 5px;` | `padding-right: 5px;`
`float: inline-start;` | `float: left;`
`inset-inline-start: 5px;` | `left: 5px;`
`border-inline-end: 1px;` | `border-right: 1px;`
`padding: 1px 2px;` | `padding: 1px 2px 1px 2px;`
`padding-block: 1px 3px;`  && `padding-inline: 4px 2px;` | `padding: 1px 2px 3px 4px;`
`text-align: start;`  or `text-align: match-parent;`, depends on the context | `text-align: left;`

When there is no special RTL-aware property available, use the pseudo `:-moz-locale-dir(ltr|rtl)` (for XUL files) or `:dir(ltr|rtl)` (for HTML files). For example, this rule:
```css
.search-box {
  background-position: 7px center
}
```

covers LTR. An additional rule is necessary to cover RTL and place the icon on the right:
```css
.search-box:dir(rtl) {
  background-position-x: right 7px;
}
```

### Mirroring Images

RTL content also affects the direction in which some icons and images are displayed, particularly those depicting a sequence of events.

#### What to mirror
* Icons that imply directionality like back/forward buttons
* Icons that imply text direction, like [readerMode.svg](https://searchfox.org/mozilla-central/rev/74cc0f4dce444fe0757e2a6b8307d19e4d0e0212/browser/themes/shared/reader/readerMode.svg)
* Icons that imply location of UI elements in the screen, like [sidebars-right.svg](https://searchfox.org/mozilla-central/rev/74cc0f4dce444fe0757e2a6b8307d19e4d0e0212/browser/themes/shared/icons/sidebars-right.svg), [open-in-new.svg](https://searchfox.org/mozilla-central/rev/74cc0f4dce444fe0757e2a6b8307d19e4d0e0212/browser/themes/shared/icons/open-in-new.svg) or [pane-collapse.svg](https://searchfox.org/mozilla-central/rev/74cc0f4dce444fe0757e2a6b8307d19e4d0e0212/devtools/client/debugger/images/pane-collapse.svg)
* Icons representing objects that are meant to be handheld should look like they're being right-handed, like the [magnifying glass icon](https://searchfox.org/mozilla-central/rev/e7c61f4a68b974d5fecd216dc7407b631a24eb8f/toolkit/themes/windows/global/icons/search-textbox.svg)
* Twisties in collapsed state, in RTL context only

#### What NOT to mirror
* Text/numbers
* Icons with text/numbers
* Icons/animations that are direction neutral
* Icons that wouldn't look differently if they'd be mirrored, like X buttons or the bookmark star icon
* Icons that should look the same as LTR, like icons related to code (which is always LTR) like [tool-webconsole.svg](https://searchfox.org/mozilla-central/rev/74cc0f4dce444fe0757e2a6b8307d19e4d0e0212/devtools/client/themes/images/tool-webconsole.svg)
* Checkmark icons
* Question mark icons
* Video/audio player controls

#### How
The most common way is:

`transform: scaleX(-1);`

### Exceptions

There are some elements that should be hardcoded to LTR, like:
* Paths
* Full URLs
* Code and code containers (like the inspector or the CSS rules panel)
* about:config preference names and values
* Telephone numbers
* Usernames & passwords (most of the time)
* Other text fields where only LTR text is expected

For such elements it's better to use left/right properties (instead of logical properties), since the direction is always determined.
A dedicated RTL rule would still be required to align the element to the correct side, if the containing element is in RTL context. See [bug 1577271](https://bugzilla.mozilla.org/show_bug.cgi?id=1577271) for example.

### Testing

To test for RTL layouts in Firefox, you can go to about:config and set `intl.uidirection` to `1`.
The Firefox UI should immediately flip, but a restart may be required to take effect in some Firefox features and interactions.

When testing with a left-to-right locale build, some text may be [displayed backwards](https://bugzilla.mozilla.org/show_bug.cgi?id=1576608#c15). This is not an issue when using an actual RTL build.

#### How to spot RTL issues

1. Punctuation marks should appear on the left side of a word/sentence/paragraph on RTL, so if a *localizable* string appears in the UI with a dot, colon, ellipsis, question or exclamation mark on the right side of the text, this probably means that the text field is forced to be displayed as LTR.
2. If icons/images/checkmarks do not appear on the opposite side of text, when compared to LTR
3. If buttons (like the close button, "OK" and "Cancel" etc.) do not appear on the opposite side of the UI, when compared to LTR
4. If paddings/margins/borders are not the same from the opposite side, when compared to LTR
5. If navigating in the UI using the left/right arrow keys does not select the correct element (i.e. pressing Left selects an item on the right)
6. If navigating in the UI using the Tab key does not focus elements from right to left
