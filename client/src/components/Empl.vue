<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>Employee</h1>
        <hr><br><br>
        <button type="button" class="btn btn-success btn-sm" v-b-modal.emp-modal>Add</button>
        <br><br>
        <table class="table table-hover">
          <thead>
            <tr>
              <th scope="col">id</th>
              <th scope="col">Name</th>
              <th scope="col">Department</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr  v-for="(emp, index) in emps" :key="index">
              <td>{{ emp.id }}</td>
              <td>{{ emp.userName }}</td>
              <td v-if="departments[emp.departmentId-1].title">
              {{departments[emp.departmentId-1].title}}</td>
              <td>
                <button
        type="button"
        class="btn btn-warning btn-sm"
        v-b-modal.emp-update-modal
        @click="editEmp(emp)">
    Edit
</button>
                <button
        type="button"
        class="btn btn-danger btn-sm"
        @click="onDeleteEmp(emp)">
    Delete
</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <b-modal ref="addEmployeeModal"
             id="emp-modal"
             title="Add a new employee"
             hide-footer>
      <b-form @submit="onSubmit" @reset="onReset" class="w-100">
        <b-form-group id="form-name-group"
                      label="Name:"
                      label-for="form-name-input">
            <b-form-input id="form-name-input"
                          type="text"
                          v-model="addEmpForm.name"
                          required
                          placeholder="Enter name">
            </b-form-input>
          </b-form-group>
        <b-form-group id="form-department-group"
                      label="Choose Department:"
                      label-for="form-department-input"><b-form-select v-model="selected" multiple >
        <option>IT</option>
        <option>Manager</option>
         </b-form-select>
        </b-form-group>
        <b-button type="submit" variant="primary">Submit</b-button>
        <b-button type="reset" variant="danger">Reset</b-button>
      </b-form>
    </b-modal>
    <b-modal ref="editEmployeeModal"
         id="emp-update-modal"
         title="Update"
         hide-footer>
  <b-form @submit="onSubmitUpdate" @reset="onResetUpdate" class="w-100">
    <b-form-group id="form-name-edit-group"
                  label="Name:"
                  label-for="form-name-edit-input">
        <b-form-input id="form-name-edit-input"
                      type="text"
                      v-model="editForm.name"
                      required
                      placeholder="Enter name">
        </b-form-input>
      </b-form-group>
      <b-form-group id="form-department-edit-group"
                      label="Choose Department:"
                      label-for="form-department-input">
                      <b-form-select v-model="selectedEdit" multiple >
        <option>IT</option>
        <option>Manager</option>
         </b-form-select>
        </b-form-group>
    <b-button type="submit" variant="primary">Update</b-button>
    <b-button type="reset" variant="danger">Cancel</b-button>
  </b-form>
</b-modal>
  </div>
</template>

<script>
import axios from 'axios';

const UsersApiPath = 'https://localhost:44329/api/users';
const DepartmentApiPath = 'https://localhost:44302/api/departments';

export default {
  data() {
    return {
      emps: [],
      addEmpForm: {
        name: '',
      },
      departments: [],
      selected: [],
      selectedEdit: [],
      editForm: {
        id: '',
        userName: '',
        departmentId: '',
      },
    };
  },
  methods: {
    getEmployee() {
      const path = UsersApiPath;
      axios.get(path)
        .then((res) => {
          this.emps = res.data;
        })
        .catch((error) => {
          // eslint-отключение следующей строки
          console.error(error);
        });
    },
    getDepartments() {
      const path = DepartmentApiPath;
      axios.get(path)
        .then((res) => {
          this.departments = res.data;
        })
        .catch((error) => {
          // eslint-отключение следующей строки
          console.error(error);
        });
    },
    addEmployee(payload) {
      const path = UsersApiPath;
      axios.post(path, payload)
        .then(() => {
          this.getEmployee();
        })
        .catch((error) => {
          // eslint-отключение следующей строки
          console.log(error);
          this.getEmployee();
        });
    },
    editEmp(emp) {
      this.editForm.id = emp.id;
      console.log(emp.id);
      this.editForm.userName = emp.userName;
      this.editForm.departmentId = emp.departmentId;
      console.log(this.editForm);
    },
    initForm() {
      this.addEmpForm.name = '';
      this.editForm.id = '';
      this.editForm.userName = '';
      this.editForm.departmentId = '';
    },
    onSubmit(evt) {
      evt.preventDefault();
      this.$refs.addEmployeeModal.hide();
      let read = 2;
      if (this.selected[0] === 'Manager') read = 1;
      console.log(this.selected);
      const payload = {
        userName: this.addEmpForm.name,
        departmentId: read, // сокращённое свойство
      };
      this.addEmployee(payload);
      this.initForm();
    },
    onSubmitUpdate(evt) {
      evt.preventDefault();
      this.$refs.editEmployeeModal.hide();
      let read = 2;
      if (this.selectedEdit[0] === 'Manager') read = 1;
      const payload = {
        id: this.editForm.id,
        userName: this.editForm.name,
        departmentId: read,
      };
      this.updateEmp(payload, this.editForm.id);
      console.log(payload);
      console.log(this.editForm.id);
    },
    onResetUpdate(evt) {
      evt.preventDefault();
      this.$refs.editEmployeeModal.hide();
      this.initForm();
      this.getEmployee();
    },
    updateEmp(payload, empID) {
      const path = `${UsersApiPath}/${empID}`;
      axios.put(path, payload)
        .then(() => {
          this.getEmployee();
        })
        .catch((error) => {
          console.error(error);
          this.getEmployee();
        });
      console.log('5');
    },
    removeEmp(empID) {
      const path = `${UsersApiPath}/${empID}`;
      axios.delete(path)
        .then(() => {
          this.getEmployee();
        })
        .catch((error) => {
          // eslint-отключение следующей строки
          console.error(error);
          this.getEmployee();
        });
    },
    onDeleteEmp(emp) {
      this.removeEmp(emp.id);
    },
    onReset(evt) {
      evt.preventDefault();
      this.$refs.addEmployeeModal.hide();
      this.initForm();
    },
  },
  created() {
    this.getEmployee();
    this.getDepartments();
  },
};
</script>
