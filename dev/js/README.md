# JavaScript knowledge

Useful notions for JS developers.

## Snippets

### Select all text of an element

Cross platform solution.

```JavaScript
element.select?.() || // all browsers except safari mobile
      element.setSelectionRange(0, element.value.length) // safari mobile
```
