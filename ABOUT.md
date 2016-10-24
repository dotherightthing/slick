# Slick Slider - assistiveTechnology update

The `assistiveTechnology` (AT) option extends the existing `accessibility` features, offering better support for AT such as screen readers.

## Configuration

Note: these options are for a slider containing `apples`.

    accessibility: true, // existing accessibility options
    assistiveTechnology: true, // new accessibility options
    centerMode: false, // centerMode focussing is not supported yet
    customPaging: function(slider, i) {
      var index = (i+1);
      return $('<button type="button" data-role="none" role="tab" tabindex="0" />').text('Set ' + index + ' of apples'); // unique and descriptive labels
    },
    dots: true, // provide consistent navigation
    focusOnSelect: true, // ?
    nextArrow: '<button type="button" data-role="none" class="slick-next" aria-label="View next set of apples" tabindex="0" role="button">Next</button>', // unique and descriptive label
    pauseOnFocus: true, // don't change the context while the user is interacting with the slider
    pauseOnHover: true, // don't change the context while the user is interacting with the slider
    prevArrow: '<button type="button" data-role="none" class="slick-prev" aria-label="View previous set of apples" tabindex="0" role="button">Previous</button>', // unique and descriptive label
    rows: 1, // multiple rows are not supported yet
    variableWidth: false // variableWidth is not supported yet

---

## Key changes

---

### Slides

---

#### options.accessibility: `role="listbox"`

* A `listbox` is a widget that allows the user to select one or more items from a list of `option`s. It is structurally equivalent to a `select` element.
* A carousel does not meet this definition of a `listbox`.

#### options.assistiveTechnology

* This `role` was removed from each slide.

---

#### default: no slide grouping

* Each slide visually 'contains' 1 or more items, but the underlying markup does not reflect this grouping.
* A single control dot may affect multiple slides. When a control dot is clicked, the slider visually updates, but the focus does not change and the user is unable to interact with the new content. This one-to-many relationship takes control away from the user and makes slider navigation abstract and unpredictable.
* `aria-live` indicates a change in the slider state, but does not guide the user directly to the new content. Multiple sliders create a lot of distracting noise.

#### options.assistiveTechnology: `role="tabpanel"`

* A new function, `buildSlideGroups`, wraps a `<div>` around each set of slides.
* This is assigned a `role` of `tabpanel`, to indicate that it is a container for the resources associated with the control dot `tab`.
* The `tabpanel` is linked to the control dot `tab` through the `aria-controls` and `aria-labelledby` attributes.
* When a control dot `tab` is clicked, the new function `setFocus` pushes the focus to the target `tabpanel`.
* The user can then navigate the slide content as they wish.
* Clone slides, added by `options.infinite`, are removed from the accessibility tree with a `role` of `presentation`

---

### Dots

---

#### default

* The control dots `tablist` follow the slides in the tab order.

#### options.assistiveTechnology

* The existing `tablist` uses absolute positioning, so its location in the DOM is flexible.
* `tablist` moved in front of the `tabpanel` elements, so that the user discovers the control dots before the controlled slides.

---

#### options.accessibility: `role="presentation"`

* The existing `<li/>` wrappers have a `role` of `presentation`. This removes them from the accessibility tree. As a result their other attributes are being ignored.

#### options.assistiveTechnology

* These attributes are relocated to the nested `button` elements.

---

#### options.accessibility: `role="toolbar"` + `role="button"`

* While a `button` describes the nature of the control, there is no semantic link to the visible slide.
* A `toolbar` is (just) a collection of commonly used function buttons or controls represented in compact visual form.

#### options.assistiveTechnology: `role="tablist"` + `role="tab"`

* The dot `role` was changed to `tab` to meet the requirements of the parent `tablist`.
* The redundant `role` of `toolbar` was removed
* The `id` was renamed to reflect the purpose of the button, `slick-navigation`, and to prevent clashes with non-slick navigation components.
* The target `id` was renamed to reflect its purpose, `slick-slideGroup`.
* The static `aria-selected` was removed, as this was causing the first button to be permanently selected. This is now managed in `updateDots()`.

---

### References

* http://www.webaxe.org/carousels-and-aria-tabs/
* https://www.sitepoint.com/unbearable-accessible-slideshow/
* http://whatsock.com/training/matrices/#listbox
* http://whatsock.com/training/matrices/#presentation
* http://whatsock.com/training/matrices/#tablist
* http://whatsock.com/training/matrices/#toolbar
* https://www.w3.org/TR/wai-aria-1.1/#tablist
* https://www.w3.org/TR/wai-aria-1.1/#tabpanel
* https://www.w3.org/TR/wai-aria-practices/#tabpanel
* https://developer.mozilla.org/en-US/docs/Web/CSS/outline
