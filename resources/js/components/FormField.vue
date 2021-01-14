<template>
    <div id="editor"></div>
</template>

<script>

import grapesjs from 'grapesjs';
import basicBlocks from 'grapesjs-blocks-basic';
import pluginNavbar from 'grapesjs-navbar';
import pluginCountdown from 'grapesjs-component-countdown';
import pluginForms from 'grapesjs-plugin-forms';
import pluginExport from 'grapesjs-plugin-export';
import pluginHeader from 'grapesjs-plugin-header';
import custom from '../plugins/custom';
import { FormField, HandlesValidationErrors } from 'laravel-nova'

export default {

    mixins: [FormField, HandlesValidationErrors],

    props: ['resourceName', 'resourceId', 'field'],

    data() {
        return {
            editor: null
        }
    },

    methods: {
        /*
         * Set the initial, internal value for the field.
         */
        setInitialValue() {
            this.value = this.field.value || ''
        },

        /**
         * Fill the given FormData object with the field's internal value.
         */
        fill(formData) {
            let newValue = '<style>' + this.editor.getCss() + '</style><div>' + this.editor.getHtml() + '</div>';
            formData.append(this.field.attribute, newValue || '')
        },

        /**
         * Update the field's internal value.
         */
        handleChange(value) {
            this.value = value
        },
    },

    mounted() {
        this.editor = grapesjs.init({
            autorender: 1,
            container: '#editor',
            storageManager: {
              type: 'remote',
              autoload: true,
              stepsBeforeSave: 1,
              urlLoad: '/nova-vendor/media-assets/'+this.resourceName+'/'+this.resourceId+'/grapesjs-load-assets',
              contentTypeJson: true,
              params: {},   // For custom values on requests
            },
            assetManager: {
              // Upload endpoint, set `false` to disable upload, default `false`
              upload: false,
              // The name used in POST to pass uploaded files, default: `'files'`
            },
            width: '100%',
             plugins: [
                 basicBlocks,
                 pluginExport,
                 pluginCountdown,
                 pluginForms,
                 pluginNavbar,
                 pluginHeader,
                 custom
             ],
            styleManager : {
                sectors: [
                    {
                        name: 'General',
                        open: false,
                        buildProps: ['float', 'display', 'position', 'top', 'right', 'left', 'bottom']
                    },{
                        name: 'Dimension',
                        open: false,
                        buildProps: ['width', 'height', 'max-width', 'min-height', 'margin', 'padding'],
                    },{
                        name: 'Typography',
                        open: false,
                        buildProps: ['font-family', 'font-size', 'font-weight', 'letter-spacing', 'color', 'line-height', 'text-shadow'],
                    },{
                        name: 'Decorations',
                        open: false,
                        buildProps: ['border-radius-c', 'background-color', 'border-radius', 'border', 'box-shadow', 'background'],
                    }
                ],
            },
        });
        this.editor.setComponents(this.field.value);
    }
}
</script>
