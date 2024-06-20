<template>
    <div class="font-bold p-4 text-2xl text-white">
        <p>Manage Users</p>
    </div>
    <div class="w-wrap flex justify-end mt-1 mb-4 mx-8 h-10">
        <ButtonDropDown :btnText="'Create User'" :items="['Single Create', 'Batch Create']" @selectedItem="handleSelectedItem"/>
    </div>
    <div class="flex justify-start items-center gap-6 mx-4 rounded-h-max shadow-lg shadow-black third-degree-form pb-4 pt-2 px-4 h-24">
        <div class="flex flex-col w-1/3">
            <label class="text-sm text-gray-200 font-bold">Who are you looking for?</label>
            <div class="relative">
                <input v-model="this.search" class="mt-2 input text-white text-sm rounded block w-full py-2.5 pl-8" placeholder="Search someone....">
                <i class="w-5 h-5 text-gray-400 inset-y-0 top-[45%] left-2 absolute" :class="ICON_PREFIX + 'search'"></i>
            </div>
        </div>
        <div class="flex flex-col w-fit h-full justify-center items-center">
            <label class="invisible">hatsus</label>
            <button @click="getAssetList()" class="rounded submit-buttons px-2 py-2 w-full text-white text-xs font-bold">
                        Search <i :class="ICON_PREFIX + 'search'" class="text-md pl-2"></i>
            </button>
        </div>
        
    </div>
    <div class="third-degree-form p-4 mx-4 rounded-h-max shadow-lg shadow-black mt-4">
        <Table :count="10" :rows="this.sampleTable" :withAction="true" @editdata="onEditDetail"
            @deletedata="onDeleteDetail" class=""/>
    </div>



    <Teleport to="body">
        <Forms_Modal v-if="form.show" :show="form.show" :subject="'User'" :action="form.action" @close="onFormClose">
            <template #body>
                <form class="flex flex-col" v-if="selectedItem === 'Single Create'" @submit.prevent>
                    <div class="self-end">
                        <label class="block text-sm font-medium leading-6 text-gray-200">
                            Database<span class="text-red-500"> *</span>
                        </label>
                        <select v-model="formData.Database"
                            class="input text-white text-sm rounded block w-full p-2.5"
                            @change="this.currentPage = 1">
                            <option class="text-sm" v-for="db in dbs" :key="db.name">
                                {{db.name}}
                            </option>
                        </select>
                    </div>
                    <div class="grid grid-cols-3 gap-4">
                        <div>
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Firstname<span class="text-red-500"> *</span>
                            </label>
                            <input v-model="formData.Firstname" type="text" class="input text-white text-sm rounded block w-full p-2.5" placeholder="Jethro">
                        </div>

                        <div>
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Middlename
                            </label>
                            <input v-model="formData.Middlename" type="text" class="input text-white text-sm rounded block w-full p-2.5">
                        </div>

                        <div>
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Lastname<span class="text-red-500"> *</span>
                            </label>
                            <input v-model="formData.Lastname" type="text" class="input text-white text-sm rounded block w-full p-2.5" placeholder="Tacdol">
                        </div>



                        <div>
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Employee ID<span class="text-red-500"> *</span>
                            </label>
                            <input v-model="formData.EmployeeID" type="number" class="input text-white text-sm rounded block w-full p-2.5 appearance-none" placeholder="12345">
                        </div>

                        <div>
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Requested By<span class="text-red-500"> *</span>
                            </label>
                            <input v-model="formData.RequestedBy" type="text" class="input text-white text-sm rounded block w-full p-2.5" placeholder="Mark Lloyd Villahermosa">
                        </div>

                        <div>
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Same Access<span class="text-red-500"> *</span>
                            </label>
                            <input v-model="formData.SameAccess" type="text" class="input text-white text-sm rounded block w-full p-2.5" placeholder="USERNAME12345">
                        </div>



                        <div class="col-span-2">
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Application<span class="text-red-500"> *</span>
                            </label>
                            <select v-model="formData.Application"
                                class="input text-white text-sm rounded block w-full p-2.5"
                                @change="this.currentPage = 1">
                                <option class="text-sm" v-for="app in apps" :key="app">
                                    {{app}}
                                </option>
                            </select>
                        </div>

                        <div>
                            <label class="block text-sm font-medium leading-6 text-gray-200">
                                Remarks<span class="text-red-500"> *</span>
                            </label>
                            <input v-model="formData.Remarks" type="text" class="input text-white text-sm rounded block w-full p-2.5" placeholder="DB Access">
                        </div>

                    </div>
                </form>
                <form v-if="selectedItem === 'Batch Create'" @submit.prevent>
                    <div class="flex">
                        <div>
                            <div class="flex justify-start gap-4">
                                <button class="submit-buttons p-2 rounded text-xs @click">Upload</button>
                                <span class="italic text-gray-600">No .xlsx file uploaded yet</span>
                            </div>
                            <div>

                            </div>
                        </div>
                        <div>

                        </div>
                    </div>
                </form>
                
            </template>
            <template #footer>
                <button class="submit-buttons p-2 rounded text-xs" @click="showModal('save')">Submit?</button>
            </template>
        </Forms_Modal>
    </Teleport>
</template>

<script>
import ButtonDropDown from '../components/smallcomponents/ButtonDropDown.vue'
import Forms_Modal from '/src/components/Forms_Modal.vue'
import { ICON_PREFIX } from '/src/assets/data/globals.json'
import { clearObject, transferArrayToObject, sleep } from '/src/assets/js/tools.js'
import Table from '/src/components/Table.vue'

export default {
    components:{
        ButtonDropDown,
        Forms_Modal,
        Table
    },
    data() {
        return {
            search: '',
            action: '',
            selectedItem: null, // Store selected item
            form: {
                show: false,
                action: 'Create'
            },
            formData: {
                Database: null,
                Firstname: null,
                Middlename: null,
                Lastname: null,
                EmployeeID: null,
                RequestedBy: null,
                SameAccess: null,
                Application: null,
                Remarks: null
            },
            confirmation: {
                show: false,
                msg: ''
            },
            alert: {
                show: false,
                msg: '',
                status: true
            },
            ICON_PREFIX,
            spinner: {
                show: false
            },




            dbs:[
                {
                    name: 'RMSPRD',
                    ip: '127.0.0.0'
                },
                {
                    name: 'RDWPRD',
                    ip: '127.0.0.0'
                },
            ],
            apps:[
                'ORMS','OREIM','ORPM','KCS Retail','Custom App(others)'
            ],
            sampleTable: [
                {
                    uid: 1,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 2,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 3,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
                {
                    uid: 4,
                    id: 12345,
                    username: "JVVILLAHERMOSAOUTSOURCE12345",
                    password: "eut020202",
                    db_name: "OUTSOURCEPRD",
                    application: "KCS Retail",
                    data_created: "01-01-2024",
                    created_by: "127.0.0.1",
                    requestor: "Maam Mimi",
                    remarks: "Outsource Access",
                    status: "Alive"
                },
            ]
        };
    },
    methods: {
        onEditDetail: function (data) {

            

            this.formData.OS = (data.OS === "Linux") ? 1 : 2
            this.formData.Type = this.selectedSearchType
            this.formData.Environment = this.selectedSearchEnv

            this.formData.Database = 
            this.formData.Firstname = 
            this.formData.Middlename = 
            this.formData.Lastname = 
            this.formData.EmployeeID = 
            this.formData.RequestedBy = 
            this.formData.SameAccess = 
            this.formData.Application = 
            this.formData.Remarks = 

            this.showForm('Edit')
        },
        onDeleteDetail: function(data){
            this.showModal('Delete')
        },
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
        showModal: function (action) {
            this.action = action
            this.confirmation.msg = 'Are you sure to ' + action + ' these details?'
            this.confirmation.show = true
        },
    },
}
</script>