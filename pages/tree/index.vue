<template>
    <div>
        <div class="columns is-gapless is-multiline is-mobile">
            <div class="column is-12">
                <h1 class="is-size-4 has-text-black">
                    <span class="has-text-weight-medium">Hi Curtis</span>, <span class="has-text-weight-light">
                        Welcome Back!</span>
                </h1>
            </div>
            <div class="column is-12">
                <nav class="breadcrumb mt-1 mb-0" aria-label="breadcrumbs">
                    <ul>
                        <li><a class="is-size-7 has-text-weight-medium has-text-link"
                               href="dashboard.html">Home</a></li>
                        <li class="is-size-7 has-text-weight-medium is-active">
                            <a href="dashboard.html"
                                aria-current="page">Dashboard</a>
                            </li>
                    </ul>
                </nav>
            </div>
        </div>

        <div class="columns is-variable is-3 is-desktop">
            <div class="column">
                <div class="card has-background-white has-text-black">
                    <header class="card-header">
                        <p class="card-header-title">
                            Trees
                        </p>
                        <p class="card-header-icon" v-if="create_tree === true">
                        <NuxtLink to="/tree/create" class="button is-link has-background-primary">
                            Create New Tree</NuxtLink>
                        </p>
                    </header>
                    <div class="card-content">
                        <template>
                            <vue-good-table
                                mode="remote"
                                :columns="columns"
                                :rows="rows"
                                @on-page-change="onPageChange"
                                @on-sort-change="onSortChange"
                                @on-column-filter="onColumnFilter"
                                @on-per-page-change="onPerPageChange"
                                @on-search="onSearch"
                                :totalRows="totalRecords"
                                :isLoading.sync="isLoading"
                                :pagination-options="{
                                perPage: 5,
                                enabled: true,
                                mode: 'pages',

                                }"
                                :sort-options="{
                                enabled: true,
                                initialSortBy: {field: 'name', type: 'asc'}
                                }"
                                :line-numbers="true"
                                :search-options="{
                                enabled: true
                                }"
                                >
                                <template slot="table-row" slot-scope="props">
                                    <span v-if="props.column.field == 'action'">
                                        <NuxtLink :to="'tree/' + rows[props.row.originalIndex].id" class="button is-link has-background-primary">
                                            Edit</NuxtLink>
                                        <button @click="deleteTree(rows[props.row.originalIndex].id)" class="button is-danger">
                                            Delete</button>
                                    </span>

                                </template>
                            </vue-good-table>
                        </template>
                    </div>
                </div>

            </div>

        </div>


    </div>
</template>

<script>
    import { mapGetters, mapActions } from "vuex";
    export default {
        layout: 'auth',
        data() {
            return {
                isLoading: false,
                totalRecords: 0,
                create_tree: false,
                columns: [
                    {
                        label: 'Name',
                        field: 'name',
                        filterOptions: {
                            enabled: true, // enable filter for this column
                            placeholder: 'Filter Name', // placeholder for filter input
                            filterValue: '', // initial populated value for this filter
                            filterDropdownItems: [], // dropdown (with selected values) instead of text input
                            filterFn: this.columnFilterFn, //custom filter function that

                        },
                    },
                    {
                        label: 'company',
                        field: 'company.name',
                        sortable: false,
                    },
                    {
                        label: 'Description',
                        field: 'description',
                        filterOptions: {
                            enabled: true, // enable filter for this column
                            placeholder: 'Filter Description', // placeholder for filter input
                            filterValue: '', // initial populated value for this filter
                            filterDropdownItems: [], // dropdown (with selected values) instead of text input
                            filterFn: this.columnFilterFn, //custom filter function that

                        },
                    },
                    {
                        label: 'Action',
                        field: 'action',
                        sortable: false,
                        width: '250px',
                    },
                ],
                rows: [],
                serverParams: {
                    columnFilters: {

                    },
                    searchTerm: {
                        searchTerm: ''
                    },
                    sort: {
                        field: 'name',
                        type: 'asc',
                    },
                    page: 1,
                    perPage: 5
                }
            };
        },
        head() {
            return {
                link: [
                    {
                        rel: 'stylesheet',
                        href: 'https://cdn.datatables.net/1.10.22/css/dataTables.bulma.min.css'
                    }
                ]
            }
        },

        computed: {
            ...mapGetters([
                    'getPerson'
            ])
        },

        methods: {
            updateParams(newProps) {
                this.serverParams = Object.assign({}, this.serverParams, newProps);
            },
            onPageChange(params) {
                this.updateParams({page: params.currentPage});
                this.loadItems();
            },

            onPerPageChange(params) {
                this.updateParams({perPage: params.currentPerPage});
                this.loadItems();
            },

            onSortChange(params) {
                console.log(params);
                this.updateParams({
                    sort: [{
                            type: params[0].type,
                            field: params[0].field,
                        }],
                });
                this.loadItems();
            },

            onColumnFilter(params) {
                this.updateParams(params);
                this.loadItems();
            },
            onSearch(params) {
                console.log(params);
                this.updateParams({searchTerm: params});
                this.loadItems();
            },
            loadItems() {
                this.$axios.$get("/api/tree", {
                    params: this.serverParams
                })
                        .then(response => {
                            this.totalRecords = response.total;
                            this.rows = response.data;
                        })
            },

            searchFunction(row, col, cellValue, searchTerm) {
                console.log(searchTerm);
            },
            deleteTree(id) {
                if (confirm("Do you really want to delete?")) {

                    this.$axios
                            .$delete("/api/tree/" + id)
                            .then(response => {
                                this.loadItems();
                            })
                }k
            },
            createTree() {
                this.$axios.$get("/api/tree/create")
                        .then(response => {
                            this.create_tree = response.create_tree
                        })
            },
        },

        created() {
            this.createTree();
        },
    }

</script>


