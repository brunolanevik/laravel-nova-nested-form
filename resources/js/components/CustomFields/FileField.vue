<script>
import FileField from '@/fields/Form/FileField.vue'
import {Errors} from 'laravel-nova'

export default {
    mixins: [FileField],
    methods: {
        /**
         * Remove the linked file from storage
         */
        async removeFile() {
            this.uploadErrors = new Errors()

            const {
                resourceName,
                resourceId,
                relatedResourceName,
                relatedResourceId,
                viaRelationship
            } = this

            const attribute = this.field.originalAttribute

            const uri = this.viaRelationship
                ? `/nova-api/${resourceName}/${resourceId}/${relatedResourceName}/${relatedResourceId}/field/${attribute}?viaRelationship=${viaRelationship}`
                : `/nova-api/${resourceName}/${resourceId}/field/${attribute}`

            try {
                await Nova.request().delete(uri)
                this.closeRemoveModal()
                this.deleted = true
                this.$emit('file-deleted')
            } catch (error) {
                this.closeRemoveModal()
                if (error.response.status == 422) {
                    this.uploadErrors = new Errors(error.response.data.errors)
                }
            }
        }
    }
}
</script>
