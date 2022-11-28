<template>
    <app-layout>
        <!-- header -->
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Students List
            </h2>
        </template>
        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg px-4 py-4">
                    <div class="bg-teal-100 border-t-4 border-teal-500 rounded-b text-teal-900 px-4 py-3 shadow-md my-3" role="alert" v-if="$page.props.flash.message">
                        <div class="flex">
                            <div>
                                <p class="text-sm">{{ $page.props.flash.message }}</p>
                            </div>
                        </div>
                    </div>

                    <button
                        @click="openForm()"
                        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded my-3">Add New Student</button>

                    <!-- table -->
                    <table class="table-fixed w-full posts-table">
                        <thead>
                        <tr class="bg-gray-100">
                            <th class="px-4 py-2 w-20">ID.</th>
                            <th class="px-4 py-2">Name</th>
                            <th class="px-4 py-2">Age</th>
                            <th class="px-4 py-2">Status</th>
                            <th class="px-4 py-2">Action</th>
                        </tr>
                        </thead>
                        <tbody>
                            <tr v-for="item in data.data">
                                <td class="px-4 py-2 border">{{ item.id }}</td>
                                <td class="px-4 py-2 border">
                                    <div class="flex items-center">
										<div class="flex-shrink-0 w-10 h-10">
											<img class="w-full h-full rounded-full"
                                                src="https://images.unsplash.com/photo-1500648767791-00dcc994a43e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2.2&w=160&h=160&q=80"
                                                alt="" />
                                        </div></div>{{ item.title }}</td>
                                <td class="px-4 py-2 border">{{ item.author }}</td>
                                <td class="px-4 py-2 border">
                                    <span
                                        class="relative inline-block px-3 py-1 font-semibold text-green-900 leading-tight">
                                        <span aria-hidden
                                            class="absolute inset-0 bg-green-200 opacity-50 rounded-full"></span>
									<span class="relative">Active</span>
									</span><!-- image --></td>
                                <td class="border px-4 py-2">
                                    <button
                                        @click="openForm(item)"
                                        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Edit</button>
                                    <button
                                        @click="deleteItem(item)"
                                        class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded">Delete</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    <pagination :links="data.links"></pagination>
                    <book-form :isOpen="isFormOpen" :isEdit="isFormEdit" :form="formObject" @formsave="saveItem" @formclose="closeModal"></book-form>
                </div>
            </div>
        </div>
    </app-layout>
</template>

<script>

    const defaultFormObject = {
        title: null, author: null, image: null
    };

    import AppLayout from './../Layouts/AppLayout.vue';
    import Pagination from './../Components/pagination.vue';
    import BookForm from './../Components/Book/form.vue';

    export default{
        props: ['data'],
        components: {
            AppLayout,
            Pagination,
            BookForm
        },
        data()
        {
            return {
                isFormOpen: false,
                isFormEdit: false,
                formObject: defaultFormObject
            }
        },
        methods: {
            saveItem(item)
            {
                let url = '/books';
                if(item.id){
                    url = '/books/' + item.id;
                    item._method = 'PUT';
                }
                this.$inertia.post(url, item, {
                    onError: () => {

                    },
                    onSuccess: () => {
                        this.closeModal();
                    }
                });
            },
            closeModal()
            {
                this.isFormOpen = false;
            },
            openForm(item)
            {
                this.isFormOpen = true;
                this.isFormEdit = !!item;
                this.formObject = item ? item : defaultFormObject;
                this.$page.props.errors = {};
            },
            deleteItem(item)
            {
                if(window.confirm('are you sure?')){
                    this.$inertia.post('/books/' + item.id, {
                        _method: 'DELETE'
                    });
                }
            }

        }

    }

</script>
