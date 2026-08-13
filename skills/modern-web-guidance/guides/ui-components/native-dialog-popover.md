# Native Dialogs and Popovers

Modern web development provides native HTML features for overlays, modals, and dropdowns, eliminating the need for heavy JavaScript libraries and complex `z-index` management. The two primary tools are the `<dialog>` element and the `popover` attribute. Both can be promoted to the Top Layer, a separate layer outside the normal document flow.

### Implementation Guidelines

* **MANDATORY:** Use the `<dialog>` element specifically for **modal** overlays that require the user's immediate attention and block interaction with the rest of the page (e.g., confirmation dialogs, critical alerts).
* **MANDATORY:** Open a modal `<dialog>` using the `.showModal()` JavaScript method, never `.show()`. `.showModal()` ensures the dialog enters the Top Layer and automatically traps keyboard focus.
* **MANDATORY:** Use the `popover` attribute for **non-modal** overlays like dropdown menus, tooltips, or command palettes that should not block the rest of the page.
* **DO** use `popover="auto"` (the default) when you want "light-dismiss" behavior (clicking outside the popover or pressing `Esc` closes it automatically).
* **DO** use `popover="manual"` for overlays that should only be closed by explicit user action (e.g., toast notifications).
* **DO** use HTML attributes to trigger popovers without JavaScript: `<button popovertarget="my-popover">Toggle</button>`.
* **DO** style the backdrop of a modal dialog using the `::backdrop` pseudo-element (e.g., `dialog::backdrop { background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); }`).

### Fallback Strategies

#### Fallbacks & browser support for Popover
Baseline status for Popover: Newly available. It's been Baseline since 2025-01-27.
Supported by: Chrome 116 (Aug 2023), Edge 116 (Aug 2023), Firefox 125 (Apr 2024), Safari 17 (Sep 2023), and Safari iOS 18.3 (Jan 2025).

**Polyfill:** To support the `popover` attribute in older browsers, conditionally load [`@oddbird/popover-polyfill`](https://github.com/oddbird/popover-polyfill). **MANDATORY:** Feature detect by checking for the `popover` property on `HTMLElement.prototype`, and load the polyfill **only** when native support is missing.

```js
// MANDATORY: Feature detect 'popover' on HTMLElement.prototype.
if (!("popover" in HTMLElement.prototype)) {
  import("@oddbird/popover-polyfill");
}
```

**Styling caveat:** The polyfill cannot define the real `:popover-open` pseudo-class, so it applies a `.\:popover-open` class instead. **MANDATORY:** Combine the two with `:is()` or `:where()`:

```css
[popover]:is(:popover-open, .\:popover-open) {
  display: block;
}
```

#### Fallbacks & browser support for `<dialog>`
Baseline status for `<dialog>`: Widely available. Supported in all major browsers since March 2022.
Supported by: Chrome 37 (Aug 2014), Edge 79 (Jan 2020), Firefox 98 (Mar 2022), Safari 15.4 (Mar 2022).

**Polyfill:** For very old legacy browsers, use the [`dialog-polyfill`](https://github.com/GoogleChrome/dialog-polyfill).

```js
import dialogPolyfill from 'dialog-polyfill';

const dialog = document.querySelector('dialog');
if (typeof HTMLDialogElement === 'undefined') {
  dialogPolyfill.registerDialog(dialog);
}
```
