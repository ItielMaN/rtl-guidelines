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

In CSS, while it's possible to apply a rule for LTR and a separate one specifically for RTL, it's usually better to use CSS Logical Properties which provide the ability to control layout through logical, rather than physical mappings.

Do | Don't do
-- | --------
`margin-inline-start: 5px;` | `margin-left: 5px;`
`padding-inline-end: 5px;` | `padding-right: 5px;`
`float: inline-start;` | `float: left;`
`inset-inline-start: 5px;` | `left: 5px;`
`border-inline-end: 1px;` | `border-right: 1px;`
`border-{start/end}-{start/end}-radius: 2px;` | `border-{top/bottom}-{left/right}-radius: 2px;`
`padding: 1px 2px;` | `padding: 1px 2px 1px 2px;`
`margin-block: 1px 3px;` && `margin-inline: 4px 2px;` | `margin: 1px 2px 3px 4px;`
`text-align: start;` or `text-align: match-parent;` (depends on the context) | `text-align: left;`

When there is no special RTL-aware property available, use the pseudo `:-moz-locale-dir(rtl)` (for XUL files) or `:dir(rtl)` (for HTML files). For example, this rule covers LTR:
```css
.search-box {
  background-position: 7px center
}
```

but an additional rule is necessary to cover RTL and place the icon on the right:
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
* Twisties in collapsed state (in RTL context only)

#### What NOT to mirror
* Text/numbers
* Icons with text/numbers
* Icons/animations that are direction neutral
* Icons that wouldn't look differently if they'd be mirrored, like X buttons or the bookmark star icon
* Icons that should look the same as LTR, like icons related to code (which is always LTR) like [tool-webconsole.svg](https://searchfox.org/mozilla-central/rev/74cc0f4dce444fe0757e2a6b8307d19e4d0e0212/devtools/client/themes/images/tool-webconsole.svg)
* Checkmark icons
* Video/audio player controls
* Product logos
* Order of size dimensions (e.g. `1920x1080` should not become `1080x1920`)
* Order of size units (e.g. `10 px` should not become `px 10` (unless the size unit is localizable))

#### How
The most common way is by flipping the X axis:

`transform: scaleX(-1);`

### Exceptions

There are some elements that should look like they would in LTR:
* Paths e.g. C:\Users\username\Desktop
* Full URLs
* Code and code containers (like the inspector or the CSS rules panel)
* about:config preference names and values
* Telephone numbers
* Usernames & passwords (most of the time)
* Other text fields where only LTR text is expected

For such elements it's better to use CSS left/right properties (instead of logical properties), since the direction is always determined.

A dedicated RTL rule would still be required to align the element to the correct side, if the containing element is in RTL context. For example, in the following screenshot both text fields (username and password) should be LTR, thus start/end properties will do the opposite effect if we want the textboxes to be nicely aligned to the right.

![about:logins textboxes](https://github.com/ItielMaN/rtl-guidelines/blob/master/about-logins-rtl.png)

### Testing

To test for RTL layouts in Firefox, you can go to about:config and set `intl.uidirection` to `1`.
The Firefox UI should immediately flip, but a restart may be required to take effect in some Firefox features and interactions.

When testing with a left-to-right locale build, you may see some oddities regarding text ordering due to the nature of displaying LTR text in RTL layout.

![about:protections RTL - English vs. Hebrew](https://github.com/ItielMaN/rtl-guidelines/blob/master/about-protections-rtl.png)

This is not an issue when using an actual RTL build.

#### How to spot RTL issues

1. Punctuation marks should appear on the left side of a word/sentence/paragraph on RTL, so if a *localizable* string appears in the UI with a dot, colon, ellipsis, question or exclamation mark on the right side of the text, this probably means that the text field is forced to be displayed as LTR.
2. If icons/images/checkmarks do not appear on the opposite side of text, when compared to LTR
3. If buttons (like the close button, "OK" and "Cancel" etc.) do not appear on the opposite side of the UI, when compared to LTR
4. If paddings/margins/borders are not the same from the opposite side, when compared to LTR
5. If navigating in the UI using the left/right arrow keys does not select the correct element (i.e. pressing Left selects an item on the right)
6. If navigating in the UI using the Tab key does not focus elements from right to left

### See Also

* [RTL Best Practices](https://docs.google.com/document/d/1Rc8rvwsLI06xArFQouTinSh3wNte9Sqn9KWi1r7xY4Y/edit#heading=h.pw54h41h12ct)
* Building RTL-Aware Web Apps & Websites: [Part 1](https://hacks.mozilla.org/2015/09/building-rtl-aware-web-apps-and-websites-part-1/), [Part 2](https://hacks.mozilla.org/2015/09/building-rtl-aware-web-apps-and-websites-part-1/)
* \<link to the PDF>

### Credits

[Google's Material Design guide for RTL](https://material.io/design/usability/bidirectionality.html)
