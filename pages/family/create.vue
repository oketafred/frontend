<template>
    <div>
        <loading :active.sync="isLoading" :color="color" :background-color="backgroundColor"> </loading>
        <div class="card">
            <header class="card-header">
                <h1 class="card-header-title">
                    Create Family
                </h1>
                <NuxtLink to="/family" class="is-size-6 is-flex has-text-link has-text-weight-medium mb-2 card-header-icon">
                    <font-awesome-icon :icon="['fas', 'angle-left']" class="mt-1 mr-2" />Back</NuxtLink>
            </header>
            <div class="card-content">
                <form>
                    <div class="field">
                        <label class="label">Description</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Description" v-model="family.description"  :class="{ 'is-danger': $v.family.description.$error }">
                        </div>
                        <p class="help" :class="{ 'is-danger': $v.family.description.$error }" v-if="!$v.family.description.required">Field is required</p>
                    </div>
                    <div class="field">
                        <label class="label">Is Active</label>
                        <div class="control">
                            <v-select label="name"  v-model="family.is_active" :reduce="family => family.id" :options="status" :class="{ 'is-danger': $v.family.is_active.$error }"></v-select>
                        </div>
                        <p class="help" :class="{ 'is-danger': $v.family.is_active.$error }" v-if="!$v.family.is_active.required">Field is required</p>
                    </div>
                    <div class="field">
                        <label class="label">Husband Id</label>
                        <div class="control">
                            <v-select label="name"  v-model="family.husband_id" :reduce="husband => husband.id" :options="male"></v-select>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Wife Id</label>
                        <div class="control">
                            <v-select label="name"  v-model="family.wife_id" :reduce="wife => wife.id" :options="female"></v-select>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Type Id</label>
                        <div class="control">
                            <v-select label="name"  v-model="family.type_id" :reduce="type => type.id" :options="types"></v-select>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Chan</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Chan" v-model="family.chan">
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Nchi</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Nchi" v-model="family.nchi">
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Rin</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="Rin" v-model="family.rin">
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
    import Loading from 'vue-loading-overlay';
    import 'vue-loading-overlay/dist/vue-loading.css';
    export default {

        layout: 'auth',
        components: {
            Loading
        },
        data() {
            return {
                error: false,
                message: "",
                description: '',
                age: 0,
                family: {
                    description: "",
                    is_active: "",
                    husband_id: "",
                    wife_id: "",
                    type_id: "",
                    chan: "",
                    nchi: "",
                    rin: "",
                },
                isLoading: true,
                fullPage: true,
                color: '#4fcf8d',
                backgroundColor: '#ffffff',
                male : [],
                female: [],
                types : [],
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
            };
        },
        validations: {
            family: {
                description: {
                    required,
                },
                is_active: {
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
                    this.$axios.$post('/api/family', this.family)
                            .then(response => (this.$router.push('/family')))
                            .catch(error => {
                            });
                }
            },
            create() {
                this.isLoading = true
                this.$axios.$get("/api/family/create")
                        .then(response => {
                           this.male = response.male
                           this.female = response.female
                           this.types = response.types
                           this.isLoading = false
                        })
            },
        },
        created() {
            this.create();
        },
    }
</script>
