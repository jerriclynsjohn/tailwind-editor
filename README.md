## Tailwind Editor 

Svelte component to generate tailwindcss html from a notion like wysiwyg editor.

This component is under development, don't use it in production yet!

![svelte tailwind wysiwyg editor](https://cdn.fouita.com/assets/fouita/images/editor-sh1.png)

## Installation

```bash
$npm i tailwind-editor
```

Import to use in a [svelte](https://svelte.dev) project, you need also [tailwind](tailwindcss.com) installed (or imported) in order for this to work

```html
<script>
    import Editor from 'tailwind-editor'
    let html = ''
</script>

<div class="flex">
    <Editor bind:html={html}>
    <div>
        {html}
    </div>
</div>
```


## Add initial HTML

If you want to add custom html when you load the component you can pass an array of elements like the following

*this is not recommended if the code is not previously generated by the editor*


```html
<script>
    import Editor from 'tailwind-editor'
    let arr_html = [
        {html: 'Hello <span class="font-bold">world!</span>', klass: 'p-2 text-3xl'}
    ]
    let html = ''
</script>

<Editor bind:html={html} {arr_html} editable />
```

## TODO
1. [ ] embed media (image, video)
2. [ ] add lists
3. [ ] drag and drop positions
4. [ ] code highlighter with tailwindcss

## Contribution

All contribution are welcome!

## About

[Fouita : UI framework for svelte + tailwind components](https://fouita.com)

