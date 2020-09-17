<template>
    <card class="total-records flex flex-col items-center justify-center">
        <div class="px-3 py-3">
            <h2 class="text-center text-3xl text-80 font-light">
                <b>{{ card.title }}:</b> {{ count | numberFormat }}
            </h2>
            <ul v-if="errors !== null" class="error">
                <template v-for="key in Object.keys(errors)">
                    <li v-for="(message, index) in errors[key]" :key="`${key}_${index}`">
                        {{ message }}
                    </li>
                </template>
            </ul>
        </div>
        
        
            <div v-if="helpText" class="absolute pin-r pin-b p-2 z-50">
                <tooltip trigger="click">
                    <icon
                        type="help"
                        viewBox="0 0 17 17"
                        height="16"
                        width="16"
                        class="cursor-pointer text-60 -mb-1"
                    />

                    <tooltip-content
                        slot="content"
                        v-html="helpText"
                        :max-width="helpWidth"
                    />
                </tooltip>
            </div>
            
    </card>
</template>

<script>
    export default {
        filters: {
            numberFormat(number) {
                return !Number.isNaN(number) ? Number(number).toLocaleString() : number
            },
        },

        props: {
            helpText: {},
            helpWidth: {},
            card: {
                type: Object,
                required: true,
            },
        },

        data() {
            return {
                count: 0,
                errors: null,
            }
        },

        mounted() {
            this.getData()
        },

        methods: {
            getData() {
                Nova.request().get("/nova-vendor/total-records/endpoint/", {
                    params: {
                        model: this.card.model,
                        expires: this.card.expires,
                    },
                })
                    .then(({ data }) => {
                        this.$set(this, "count", data.count)
                    })
                    .catch(({ response }) => {
                        this.$set(this, "errors", response.data.errors)
                    })
            },
        },
    }
</script>
