<template>
    <div class="font-bold p-4 text-2xl text-white">
        <p>Manage Users</p>
    </div>
    <div class="w-wrap flex justify-end mt-1 mb-4 mx-8 h-10">
        <ButtonDropDown :btnText="'Create User'" :items="['Single Create', 'Batch Create']" @selectedItem="handleSelectedItem"/>
    </div>



    <Teleport to="body">
        <Forms_Modal v-if="form.show" :show="form.show" :subject="'User'" :action="form.action" @close="onFormClose">
            <template #body>
                <form v-if="selectedItem === 'Single Create'" @submit.prevent>
                    <div >
                        Single
                    </div>
                </form>
                <form v-if="selectedItem === 'Batch Create'" @submit.prevent>
                    <div >
                        Batch
                    </div>
                </form>
                
            </template>
        </Forms_Modal>
    </Teleport>
</template>

<script>
import ButtonDropDown from '../components/smallcomponents/ButtonDropDown.vue'
import Forms_Modal from '/src/components//Forms_Modal.vue'
import { clearObject, transferArrayToObject, sleep } from '/src/assets/js/tools.js'

export default {
    components:{
        ButtonDropDown,
        Forms_Modal
    },
    data() {
        return {
            selectedItem: null, // Store selected item
            form: {
                show: false,
                action: 'Create'
            },
            formData: {
                
            },
        };
    },
    methods: {
        handleSelectedItem(item) {
            // Handle selected item from ButtonDropDown
            console.log('Selected item:', item);
            this.form.show = true;
            this.selectedItem = item; // Update selected item for use in Forms_Modal
        },
        onFormClose: function (event) {
            if (event) {
                this.form.show = false
                this.clearForm()
            }
        },
        showForm: function (action) {
            this.form.action = action
            this.form.show = true
        },
        clearForm: function () {
            clearObject(this.formData)
        },
    },
}
</script>