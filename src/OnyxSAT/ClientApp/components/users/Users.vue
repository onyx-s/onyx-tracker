<template>
    <div class="container d-flex flex-column">
        <h1 class="display-4 align-self-center">All Users</h1>
        <div class="content-row">
            <alert v-if="alert" :message="alert"></alert>
            <!--
            <div class="dropdown">
                <button class="btn-default dropdown-toggle" type="button" data-toggle="dropdown">Filter by block
                    <span class="caret"></span>
                </button>
                <ul class="dropdown-menu">
                    <li>A</li>
                    <li>B</li>
                    <li>C</li>
                </ul>
            </div>
            -->
            <router-link to="/users/add" tag="button" class="btn btn-default">Add User</router-link>
            <button id="btn-Delete" tag="button" class="btn btn-danger float-right" data-toggle="modal" data-target=".bs-example-modal-sm" disabled>Delete Selected</button>
        </div>
        <div class="modal bs-example-modal-sm" tabindex="-1" role="dialog" aria-labelledby="mySmallModalLabel">
            <div class="modal-dialog modal-sm" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <a>Are you sure?</a>
                    </div>
                    <div class="modal-footer">
                        <button  class="btn btn-default" data-dismiss="modal">Cancel</button>
                        <button v-on:click="deleteUsers()" class="btn btn-primary" data-dismiss="modal">Yes</button>
                    </div>
                </div>
            </div>
        </div>
        <table class="table table-bordered mb-5">
            <thead class="thead-default">
                <tr>
                    <th>ID</th>
                    <th>Firstname</th>
                    <th>Lastname</th>
                    <th>Email</th>
                    <th>Mobile</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="u in users">
                    <td>
                        <router-link :to="u.userId.toString()" append>{{ u.userId }}</router-link>
                    </td>
                    <td>{{ u.firstName }}</td>
                    <td>{{ u.lastName }}</td>
                    <td>{{ u.email }}</td>
                    <td>{{ u.mobile }}</td>
                    <td><input type="checkbox" class="align-self-center" :id="u.userId" @click="toggleCheckbox(u.userId), btnDisable()"/></td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import Alert from '../Alert'
export default {
    data() {
        return {
            checkedNames: [],
            users: [],
            alert: ''
        }
    },
    methods: {
        disableLoading() {
            document.getElementById("loading").style.display = "none";
        },
        //Fetches the list of users from database
        getUsers() {
            this.axios.get('/api/users/')
                .then(response => this.users = response.data)
                .catch(error => console.log(error))
        },
        //Deletes the selected users
        deleteUsers() {
            for (let i = 0; i < this.checkedNames.length; i++) {
                this.axios.delete('/api/users/' + this.checkedNames[i]);
            }
            //Refresh the page and alert that users were deleted
            //this.$router.push({ name: 'users', params: { alert: 'User Added' } });
            document.getElementById("loading").style.display = "block";
            location.reload(true);
        },
        //Adds checked items to array of items to delete
        toggleCheckbox(id) {
            if (document.getElementById(id).checked === true) {
                this.checkedNames.push(id);
            } else {
                this.checkedNames.splice(this.checkedNames.indexOf(id),1);
            }
        },
        //Toggles the delete users button depending on checked boxes
        btnDisable() {
            let e_id = event.target;
            let e_btn = document.getElementById('btn-Delete');
            if (e_id.checked === true) {
                e_btn.disabled = false;
                e_btn.active = true;
            } else if (e_id.checked === false) {
                e_btn.active = false;
                e_btn.disabled = true;
            }
        }
    },
    created() {
        if (this.$route.params.alert)
            this.alert = this.$route.params.alert;
        this.getUsers();
        this.disableLoading();
    },
    components: {
        Alert
    }
}
</script>

<style>
.dropdown {
    display: inline;
}
.content-row {
    display: inline;
    padding-bottom: 15px;
}
.add-student {
    display: inline;
    position: relative;
    width: 150px;
    align-self: right;
}
</style>