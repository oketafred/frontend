<template>
    <div>
        <div class="card">
            <header class="card-header">
                <h1 class="card-header-title">
                    Create Note
                </h1>
                <NuxtLink to="/note" class="is-size-6 is-flex has-text-link has-text-weight-medium mb-2 card-header-icon">
                    <font-awesome-icon :icon="['fas', 'angle-left']" class="mt-1 mr-2" />Back</NuxtLink>
            </header>
            <div class="card-content">
                <form>
                    <div class="field">
                        <label class="label">Gid</label>
                        <div class="control">
                             <v-select label="name" multiple  v-model="note.gid
                            " :reduce="note => note.id" :options="options"></v-select>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Note</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Note" v-model="note.note">
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Rin</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Rin" v-model="note.rin">
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Name</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Name" v-model="note.name" :class="{ 'is-danger': $v.note.name.$error }">
                        </div>
                        <p class="help" :class="{ 'is-danger': $v.note.name.$error }" v-if="!$v.note.name.required">Field is required</p>
                    </div>
                    <div class="field">
                        <label class="label">Description</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Description" v-model="note.description">
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Is Active</label>
                        <div class="control">
                            <v-select label="name"  v-model="note.is_active" :reduce="note => note.id" :options="status"></v-select>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Type Id</label>
                        <div class="control">
                            <v-select label="name"  v-model="note.type_id
                            " :reduce="note => note.id" :options="options" ></v-select>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Group</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Group" v-model="note.group" >
                        </div>
                    </div>
                    <div class="field is-grouped">
                        <div class="control">
                            <button @click.prevent="save()" class="button is-link has-background-primary">Submit</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>

    </div>
</template>

<script>
    import { required } from 'vuelidate/lib/validators'
    export default {
        layout: 'auth',
        data() {
            return {
                error: false,
                message: "",
                note: {
                    gid: "",
                    note: "",
                    rin: "",
                    name: "",
                    description: "",
                    is_active: "",
                    type_id: "",
                    group: "",
                },
                options : [
                  {
                    id: 1,
                    name: "HTML5",
                  },
                  {
                    id: 2,
                    name: "HTML5",
                  },
                  {
                    id: 3,
                    name: "HTML5",
                  },
                ],
                 status : [
                  {
                    id: 1,
                    name: "Active",
                  },
                  {
                    id: 0,
                    name: "Inactive",
                  },
                ],
                type: [],
            };
        },
        validations: {
            note: {
                name: {
                    required,
                },
            },
        },
        methods: {
            save() {
                this.$v.$touch();
                if (this.$v.$invalid) {
                    console.log("fail")
                } else {
                    this.$axios.$put('/api/note/' + this.$route.params.id, this.note)
                            .then(response => (this.$router.push('/note')))
                            .catch(error => {
                            });
                }
            },
            getType() {
                this.$axios.$get("/api/alltype")
                .then(response => {
                    this.type = response;
                })
            },
        },

        async asyncData( { $axios, params }) {
            const note = await $axios.$get('/api/note/' + params.id)
            return {note}
        },
          created() {
            this.getType()
        }
    }
</script>
