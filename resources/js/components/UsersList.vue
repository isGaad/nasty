<template>
    <div class="row">
        <div class="table-filters col-md-12 mb-4">
            <div class="row">
                <div class="col-sm-6 col-md-3 mb-1">
                    <div class="input-group  search-area d-xl-inline-flex  mr-1" >
                        <input type="text" v-on:keyup.enter="fetchUsers()" class="form-control" placeholder="Search here..." v-model="q">
                        <div class="input-group-append">
                            <span class="input-group-text">
                                <a href="javascript:void(0)" v-on:click="fetchUsers()"><i class="flaticon-381-search-2"></i></a>
                            </span>
                        </div>
                    </div>
                </div>
                <div class="col-md-2 col-sm-6 mb-1">
                    <div class="input-group search-area d-xl-inline-flex ">
                        <div class="dropdown " style="width: 100%">
                            <a href="javascript:void(0)" :class="'btn-'+status.class" class="btn btn-rounded " style="width: 100%" data-toggle="dropdown" aria-expanded="false">
                                <i class="las la-bolt scale5"></i>
                                {{  status.label }}
                                <i class="las la-angle-down "></i>
                            </a>
                            <div class="dropdown-menu dropdown-menu-center">
                                <a v-for="state in statuses" v-on:click="changeStatus(state)" class="dropdown-item" href="javascript:void(0);"><span :class="'text-'+state.class">{{ state.label }}</span></a>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col-md-2 col-sm-6 mb-1">
                    <div class="input-group search-area d-xl-inline-flex ">
                        <div class="dropdown " style="width: 100%">
                            <a href="javascript:void(0)" :class="'btn-'+role.class" class="btn btn-rounded " style="width: 100%" data-toggle="dropdown" aria-expanded="false">
                                <i class="las la-bolt scale5"></i>
                                {{  role.label }}
                                <i class="las la-angle-down "></i>
                            </a>
                            <div class="dropdown-menu dropdown-menu-center">
                                <a v-for="userRole in roles" v-on:click="changeRole(userRole)" class="dropdown-item" href="javascript:void(0);"><span :class="'text-'+userRole.class">{{ userRole.label }}</span></a>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col-md-2 col-sm-6 mb-1 ml-auto">
                    <div class="input-group search-area d-xl-inline-flex">
                                <div class="dropdown " style="width: 100%">
                            <a href="javascript:void(0)" :class="'btn-'+sort.class" class="btn btn-rounded " style="width: 100%" data-toggle="dropdown" aria-expanded="false">
                                <i class="las la-sort-amount-down-alt scale5"></i>

                                {{  sort.label }}
                                <i class="las la-angle-down "></i>
                            </a>
                            <div class="dropdown-menu dropdown-menu-center">
                                <a v-for="userSort in sorts" v-on:click="changeSort(userSort)" class="dropdown-item" href="javascript:void(0);"><span :class="'text-'+userSort.class">{{ userSort.label }}</span></a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="col-lg-12">
            <div class="card">
                <div class="card-body">
                    <div class="table-responsive">
                        <table class="table table-responsive-md text-small">
                            <thead>
                                <tr>
                                    <th class="width10">#</th>
                                    <th>Name</th>
                                    <th>Email</th>
                                    <th>Status</th>
                                    <th>Role</th>
                                    <th>Registration Date</th>
                                    <th></th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="user in users.data">
                                    <td><strong>{{ user.id }}</strong></td>
                                    <td>{{ user.name }}</td>
                                    <td>{{ user.email}}</td>
                                    <td>
                                        <span v-bind:class="{ 'badge-success' : (user.active === 2) , 'badge-light' : (user.active === 1) }" class="badge">{{ user.statusLabel }}</span>
                                    </td>
                                    <td>
                                        <span v-bind:class="{ 'badge-secondary' : (user.role === 2), 'badge-primary text-white' : (user.role === 1) }" class="badge">{{ user.roleLabel }}</span>
                                    </td>
                                    <td>{{ moment(user.created_at).format('DD MMM YYYY, hh:mm A') }}</td>
                                    <td>
                                        <div class="dropdown">
                                            <button type="button" class="btn btn-success light sharp" data-toggle="dropdown">
                                                <svg width="20px" height="20px" viewBox="0 0 24 24" version="1.1"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><rect x="0" y="0" width="24" height="24"/><circle fill="#000000" cx="5" cy="12" r="2"/><circle fill="#000000" cx="12" cy="12" r="2"/><circle fill="#000000" cx="19" cy="12" r="2"/></g></svg>
                                            </button>
                                            <div class="dropdown-menu">
                                                <a class="dropdown-item" :href="route('admin.users.edit', {user: user.id, page: page})">Edit</a>
                                            </div>
                                        </div>
                                    </td>
                                </tr>

                            </tbody>
                        </table>
                        <pagination :limit="8" :data="users" :show-disabled="true" @pagination-change-page="fetchUsers">
                            <span slot="prev-nav">&lt;</span>
                            <span slot="next-nav">&gt;</span>

                        </pagination>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'UsersList',
    data: function () {
        return {
            'q' : '',
            'status': { 'id' : 0, 'label' : 'All Statuses', 'class' : ''},
            'role': { 'id' : 0, 'label' : 'All Roles', 'class' : ''},
            'sort': { 'id' : 'created_at', 'direction': 'DESC', 'label' : 'Sort', 'class' : ''},
            'users' : {},
            'page' : 1,
            'statuses' : [],
            'roles' : [],
            'sorts' : []
        }
    },
    created() {
        this.fetchUsers();
        this.fetchStatuses();
        this.fetchRoles();
        this.fetchSorts();
    },
    computed: {
    },
    methods: {
        fetchUsers(page = 1) {
            this.page = page;
            axios
                .get(route('api.users.index', {
                    page: page,
                    q : this.q,
                    status : this.status.id,
                    role: this.role.id,
                    sort : this.sort.id,
                    direction : this.sort.direction,
                }))
                .then(response => {
                    this.users = response.data;
                });
        },
        fetchStatuses() {
            this.statuses = {
                0: { 'id' : 0, 'label' : 'All Statuses', 'class' : ''},
                1: { 'id' : 1, 'label' : 'Disabled', 'class' : 'primary'},
                2: { 'id' : 2, 'label' : 'Active', 'class' : 'success'},
            };
        },
        fetchRoles() {

            this.roles = {
                0: { 'id' : 0, 'label' : 'All Roles', 'class' : ''},
                1: { 'id' : 1, 'label' : 'Customer', 'class' : 'primary'},
                2: { 'id' : 2, 'label' : 'Administrator', 'class' : 'secondary'},
            };
        },
        fetchSorts() {
            this.sorts = {
                1: { 'id' : 'created_at', 'direction': 'DESC', 'label' : 'Created DESC', 'class' : 'dark'},
                2: { 'id' : 'created_at', 'direction': 'ASC', 'label' : 'Created ASC', 'class' : 'dark'},
            };
        },
        changeStatus(status) {
            this.status = (status !== undefined) ? this.status = status : '';
            this.fetchUsers();
        },
        changeRole(role) {
            this.role = (role !== undefined) ? this.role = role : '';
            this.fetchUsers();
        },
        changeSort(sort) {
            console.log(sort);
            this.sort = (sort !== undefined) ? this.sort = sort : '';
            this.fetchUsers();
        },
    }
}
</script>
