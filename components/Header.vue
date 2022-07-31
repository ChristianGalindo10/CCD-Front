<template>
    <div>
        <b-navbar toggleable="lg" type="dark" variant="danger" sticky:true>

            <b-navbar-brand>
                <NuxtLink to="/" class="navbar-brand">
                    CCD
                </NuxtLink>
            </b-navbar-brand>

            <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

            <b-collapse id="nav-collapse" is-nav>
                <b-navbar-nav>
                    <li class="nav-item">
                        <NuxtLink to="/Recomendado" class="nav-link">
                            Recomendado |
                        </NuxtLink>
                    </li>
                    <li class="nav-item">
                        <NuxtLink to="/Especialidades" class="nav-link">
                            Especialidades |
                        </NuxtLink>
                    </li>
                    <li class="nav-item">
                        <NuxtLink to="/Restaurantes" class="nav-link">
                            Restaurantes |
                        </NuxtLink>
                    </li>
                    <li class="nav-item">
                        <NuxtLink to="/Productos" class="nav-link">
                            Productos |
                        </NuxtLink>
                    </li>
                </b-navbar-nav>

                <!-- Right aligned nav items -->
                <b-navbar-nav class="ml-auto">
                    <b-nav-form>
                        <!-- <b-form-input size="sm" class="mr-sm-2" placeholder="Search"></b-form-input> -->
                        <!-- <b-button size="sm" class="my-2 my-sm-0" type="submit">Search</b-button> -->
                        <!-- <NuxtLink class="btn my-2 my-sm-0 btn-secondary btn-sm" to="/sign_in">
                            Sign In
                        </NuxtLink> -->
                        <NuxtLink v-if="!logIn" class="nav-link" to="/Log_in">
                            Ingresar
                        </NuxtLink>
                        <NuxtLink v-if="logIn" class="nav-link" to="/User">
                            {{name}}
                        </NuxtLink>
                        <a href="#" v-if="logIn" class="nav-link" @click="removeToken">Cerrar Sesión</a>
                        <!-- <button >Cerrar Sesión</button> -->
                    </b-nav-form>

                    <!-- <b-nav-item-dropdown text="Lang" right>
                        <b-dropdown-item href="#">EN</b-dropdown-item>
                        <b-dropdown-item href="#">ES</b-dropdown-item>
                        <b-dropdown-item href="#">RU</b-dropdown-item>
                        <b-dropdown-item href="#">FA</b-dropdown-item>
                    </b-nav-item-dropdown> -->

                    <!-- <b-nav-item-dropdown right>
                         Using 'button-content' slot 
                        <template #button-content>
                            <em>User</em>
                        </template>
                        <b-dropdown-item href="#">Profile</b-dropdown-item>
                        <b-dropdown-item href="#">Sign Out</b-dropdown-item>
                    </b-nav-item-dropdown> -->
                </b-navbar-nav>
            </b-collapse>
        </b-navbar>
    </div>
</template>

<script>
export default {
    name: 'Header',
    mounted() {
        if (localStorage.getItem('token') != null) {
            this.$axios.defaults.headers.common['Authorization'] = 'Bearer ' + localStorage.getItem('token');
            this.name = localStorage.getItem('userName')
            
            this.logIn = true
            console.log(this.logIn)
        }
    },
    data() {
        return {
            logIn: false,
            name: ''
        }
    },
    methods:{
        removeToken(){
            localStorage.clear()
            this.logIn = false
            this.$router.push('/')
            
        }
    }
}
</script>