<template>
  <div class="Dashboard">
    <div class="row w-100">
      <div class="col-lg-6 offset-lg-3">
        <div class="controls mt-5 mb-2">
          <div class="search-bar">
            <div class="input-group">
              <input
                type="text"
                class="form-control"
                placeholder="Enter name to search"
              />
              <button type="button" class="btn btn-primary">Search</button>
            </div>
          </div>
          <div class="add-user d-grid gap-2 col-3 mx-auto my-3">
            <button
              data-bs-toggle="modal"
              data-bs-target="#staticBackdrop"
              type="button ms-e"
              class="btn btn-primary"
            >
              + <span class="ms-2">Add New User</span>
            </button>
          </div>
        </div>
        <!-- SPINNER -->
        <div v-if="loading" class="d-flex justify-content-center">
          <div class="spinner-border" role="status">
            <span class="visually-hidden">Loading...</span>
          </div>
        </div>
        <!-- Listing users -->
        <p class="text-muted text-center">
          <small>User 's Found - {{ users.length }}</small>
        </p>
        <div class="list py-3 position-sticky">
          <ol class="list-group">
            <li
              v-for="user in users"
              :key="user._id"
              class="
                list-group-item list-group-item-action
                d-flex
                justify-content-between
                align-items-start
              "
            >
              <div class="ms-2 me-auto">
                <div class="fw-bold">
                  {{ user.firstname }} {{ user.lastname }}
                </div>
                {{ user.email }}
              </div>
              <span
                v-if="user.adminIs == 'HOD'"
                class="badge bg-danger rounded-pill"
                >{{ user.adminIs }}</span
              >
              <span
                v-if="user.adminIs == 'WARDEN'"
                class="badge bg-primary rounded-pill"
                >{{ user.adminIs }}</span
              >
              <span
                v-if="user.adminIs == 'COMMON'"
                class="badge bg-dark rounded-pill"
                >{{ user.adminIs }}</span
              >
              <span
                v-if="user.adminIs == 'SUPER'"
                class="badge bg-warning text-dark rounded-pill"
                >{{ user.adminIs }}</span
              >
            </li>
          </ol>
        </div>
      </div>
    </div>

    <!-- MODAL to create new user -->
    <div
      class="modal fade"
      id="staticBackdrop"
      data-bs-backdrop="static"
      data-bs-keyboard="false"
      tabindex="-1"
      aria-labelledby="staticBackdropLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="staticBackdropLabel">
              Add New Admin User
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="save">
              <div class="row mb-3">
                <div class="col">
                  <label for="role" class="form-label">Role</label>
                  <select
                    v-model="form_data.adminIs"
                    required
                    id="role"
                    class="form-select"
                  >
                    <option value="WARDEN">Warden</option>
                    <option value="HOD">HOD</option>
                    <option value="COMMON">Common</option>
                  </select>
                </div>
              </div>
              <div v-if="form_data.adminIs" class="form-input-wrapper">
                <div class="row mb-3">
                  <div class="col">
                    <input
                      v-model="form_data.firstname"
                      type="text"
                      class="form-control"
                      placeholder="First name"
                      aria-label="First name"
                      required
                    />
                  </div>
                  <div class="col">
                    <input
                      v-model="form_data.lastname"
                      type="text"
                      class="form-control"
                      placeholder="Last name"
                      aria-label="Last name"
                      required
                    />
                  </div>
                </div>
                <div class="row mb-3">
                  <div class="col">
                    <label for="contact" class="form-label">Contact</label>
                    <input
                      v-model="form_data.contact"
                      id="contact"
                      type="tel"
                      pattern="[0-9]{10}"
                      class="form-control"
                      placeholder="Phone No."
                      maxlength="10"
                      required
                    />
                  </div>
                  <div class="col">
                    <label for="department" class="form-label"
                      >Department</label
                    >
                    <select
                      v-model="form_data.department"
                      required
                      id="department"
                      class="form-select"
                      aria-label="Default select example"
                    >
                      <option value="BOYS_HOSTEL">Boys Hoetel</option>
                      <option value="GIRLS_HOSTEL">Girls Hostel</option>
                      <option value="CSE">CSE</option>
                      <option value="EEE">EEE</option>
                      <option value="META">META</option>
                      <option value="MECH">MECH</option>
                    </select>
                  </div>
                </div>
                <div class="row mb-3">
                  <div class="col">
                    <label for="id" class="form-label">Employee Code</label>
                    <input
                      required
                      v-model="form_data.employeeCode"
                      id="id"
                      type="text"
                      class="form-control"
                      placeholder="Enter Employee Code"
                    />
                  </div>
                </div>
                <div class="row mb-3">
                  <div class="col">
                    <label for="email" class="form-label">Email Address</label>
                    <input
                      v-model="form_data.email"
                      required
                      id="email"
                      type="email"
                      class="form-control"
                      placeholder="Email Address"
                    />
                  </div>
                </div>
              </div>
              <div class="modal-footer">
                <button
                  type="button"
                  class="btn btn-secondary"
                  data-bs-dismiss="modal"
                >
                  Cancel
                </button>
                <button type="submit" class="btn btn-primary">Add User</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import qs from "qs";
export default {
  data() {
    return {
      loading: true,
      saving: false,
      users: [],
      form_data: {
        firstname: "",
        lastname: "",
        email: "",
        adminIs: "",
        contact: "",
        employeeCode: "",
        department: "",
      },
    };
  },
  beforeCreate() {
    this.loading = true;
    this.axios.get("http://10.60.200.14:3000/api/admin/list").then((res) => {
      console.log(res.data.users);
      this.users = res.data.users;
      this.loading = false;
    });
  },
  methods: {
    save() {
      const config = {
        headers: {
          "Content-Type": "application/x-www-form-urlencoded",
        },
      };
      let user = this.form_data;
      user.contact = parseInt(user.contact);
      user.password = user.employeeCode;
      console.log(user);
      // console.log(user);
      this.axios
        .post(
          "http://10.60.200.14:3000/api/admin/add-user",
          qs.stringify(user),
          config
        )
        .then((res) => {
          console.log(res);
          alert(res.data.msg);
        });
    },
  },
};
</script>

<style lang="scss" scoped></style>
