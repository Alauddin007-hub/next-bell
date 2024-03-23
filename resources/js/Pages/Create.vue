<script>
import vueDropzone from "dropzone-vue3";
import { useForm } from "@inertiajs/vue3";

export default {
    components: {
        vueDropzone,
    },
    setup() {
        const form = useForm({
            files: [], // Store multiple files in an array
        });

        const submit = () => {
            // Create FormData object
            const formData = new FormData();
            // Append CSRF token to form data
            formData.append('_token', document.querySelector('meta[name="csrf-token"]').getAttribute('content'));
            // Append files to form data
            form.files.forEach(file => formData.append('files[]', file));

            // Send POST request
            fetch('/file/upload', {
                method: 'POST',
                body: formData,
            })
            .then(response => {
                if (!response.ok) {
                    throw new Error('Network response was not ok');
                }
                return response.json();
            })
            .then(data => {
                alert("Files uploaded successfully!");
                form.reset();
            })
            .catch(error => {
                console.error('Error:', error);
                alert("File upload failed!");
            });
        };

        return {
            form,
            submit,
            dropzoneOptions: {
                url: "#", 
                thumbnailWidth: 150,
                maxFilesize: 10240,
                headers: { "My-Awesome-Header": "header value" },
                addRemoveLinks: true, // Optional: Adds remove links for each file
                uploadMultiple: true, // Enable multiple file uploads
            },
        };
    },
};
</script>





<template>
    <div class="container">
        <form @submit.prevent="submit" enctype="multipart/form-data">
            
            <h3>Dropzone File Upload</h3>
            <vue-dropzone
                ref="myVueDropzone"
                id="dropzone"
                v-model:files="form.files"
                :options="dropzoneOptions"
            />
        </form>
    </div>
</template>

<style></style>
