Sure, here's the guide to integrate CKEditor with Nuxt 3:

1. Prepare CKEditor Interface
Access the CKEditor online builder to create your interface. https://ckeditor.com/ckeditor-5/online-builder/
After creating, download and copy the build directory into the public directory of your Nuxt 3 project.
You may choose to copy the CSS files or not, depending on your needs. If copying, place them in a sample directory.
2. Create CKEditor Component
Create a new component named ckeditor.vue in the components directory of your project.
3. Configure CKEditor in the Component

components/editor.vue

```

<template>
    <div id="editor">
    </div>
</template>
<script setup>
const props = defineProps({
    content: {
        type: String,
        default: '<p class="h-80">Nhap noi dung</p>'
    }
})
const editorData = computed(() => props.content)
const emit = defineEmits("change-data");
useHead({
    css: ["/build/style.css"],
    script: [{
        "src": "/build/ckeditor.js"
    }]
})
onMounted(() => {
    if (process.client) {
        ClassicEditor
            .create(document.querySelector('#editor'), {
                // Editor configuration.
            })
            .then(editor => {
                window.editor = editor;
                editor.setData(editorData.value);
                // https://ckeditor.com/.../getting-and-setting-data.html...
                editor.model.document.on('change:data', () => {
                    emit("change-data", editor.getData())
                });
            })
            .catch((error) => {
                console.log(error);
                // const issueUrl = 'https://github.com/ckeditor/ckeditor5/issues';
                // const message = [
                // 'Oops, something went wrong!',
                // `Please, report the following error on ${issueUrl} with the build id "4rwrpk8dm24-805bmyycq64" and the error stack trace:`
                // ].join('\n');
            });
    }
})
</script>
<style scoped>
.ck-editor__main,
.ck-content {
    min-height: 480px;
}
</style>
```

4. Use CKEditor
Use the ckeditor.vue component in the app header or in any page where you want to use it.
Pass initial data through the content prop, and use emit to retrieve data when changes occur.

app.vue

```
<template>
  <div>
    <ckeditor :content="initialContent" @change-data="handleChangeData" />
  </div>
</template>

<script setup>
import Ckeditor from '~/components/ckeditor.vue';

const initialContent = ref('<p>Hello, CKEditor!</p>');

const handleChangeData = newData => {
  console.log('New data:', newData);
  // Handle new data when changes occur.
};
</script>

```
