<template>
  <div>
    <div
      :class="['bg-overlay', showOverlay ? 'active' : '']"
      @click="closeOverlay"
    ></div>
    <!-- add employee modal -->
    <div v-if="showAddEmployeeModal" class="add-employee-modal modal">
      <img
        src="../assets/close-button.svg"
        alt="Close icon"
        class="close-img"
        @click="closeOverlay"
      />
      <h2>Add employee</h2>
      <div class="input-fields">
        <div class="label-input">
          <label>First name</label>
          <input type="text" v-model="newEmployeeFirstName" />
        </div>
        <div class="label-input">
          <label>Last name</label>
          <input type="text" v-model="newEmployeeLastName" />
        </div>
        <div class="label-input">
          <label>Job title</label>
          <input type="text" v-model="newEmployeeTitle" />
        </div>
        <div class="label-input">
          <label>Job function</label>
          <input type="text" v-model="newEmployeeJobFunction" />
        </div>
      </div>
      <h2>Contact Information</h2>
      <div class="input-fields">
        <div class="label-input">
          <label>Email</label>
          <input type="email" v-model="newEmployeeEmail" />
        </div>
        <div class="label-input">
          <label>Phone number</label>
          <input type="text" v-model="newEmployeePhoneNumber" />
        </div>
      </div>
      <button @click="AddNewEmployee">Add new employee</button>
    </div>
    <!-- remove employee modal -->
    <div v-if="showRemoveEmployeeModal" class="remove-employee-modal modal">
      <img
        src="../assets/close-button.svg"
        alt="Close icon"
        class="close-img"
        @click="closeOverlay"
      />
      <h2>
        Are you sure you want to remove
        <span class="semi-bold">{{ selectedEmployee }}</span> from the list?
      </h2>
      <button @click="removeEmployee">Yes</button>
      <button class="no-btn" @click="closeRemoveEmployeeModal">No</button>
    </div>
    <!-- employee details modal -->
    <div
      v-if="showEmployeeDetailsModal && !showEditModal"
      class="employee-details-modal modal"
    >
      <img
        src="../assets/close-button.svg"
        alt="Close icon"
        class="close-img"
        @click="closeOverlay"
      />
      <h2>
        Information about <span class="semi-bold">{{ selectedEmployee }}</span>
      </h2>
      <div class="employee-datails">
        <div class="details-label">
          <p class="label">First name</p>
          <p>
            {{
              selectedEmployeeData.firstName !== ""
                ? selectedEmployeeData.firstName
                : "-"
            }}
          </p>
        </div>
        <div class="details-label">
          <p class="label">Last name</p>
          <p>
            {{
              selectedEmployeeData.lastName !== ""
                ? selectedEmployeeData.lastName
                : "-"
            }}
          </p>
        </div>
        <div class="details-label">
          <p class="label">Job title</p>
          <p>
            {{
              selectedEmployeeData.title !== ""
                ? selectedEmployeeData.title
                : "-"
            }}
          </p>
        </div>
        <div class="details-label">
          <p class="label">Job function</p>
          <p>
            {{
              selectedEmployeeData.jobFunction !== ""
                ? selectedEmployeeData.jobFunction
                : "-"
            }}
          </p>
        </div>
        <div class="details-label">
          <p class="label">Email</p>
          <a :href="`mailto:${selectedEmployeeData.email}`">{{
            selectedEmployeeData.email !== "" ? selectedEmployeeData.email : "-"
          }}</a>
        </div>
        <div class="details-label">
          <p class="label">Phone number</p>
          <a :href="`tel:${selectedEmployeeData.phoneNumber}`">{{
            selectedEmployeeData.phoneNumber !== ""
              ? selectedEmployeeData.phoneNumber
              : "-"
          }}</a>
        </div>
      </div>
      <button @click="showEditModal = true">Edit employee</button>
    </div>
    <!-- edit employee modal -->
    <div v-if="showEditModal" class="edit-employee-modal modal">
      <img
        src="../assets/close-button.svg"
        alt="Close icon"
        class="close-img"
        @click="closeOverlay"
      />
      <h2>
        Edit <span class="semi-bold">{{ selectedEmployee }}</span> information
      </h2>
      <div class="employee-datails">
        <div class="details-label">
          <p class="label">First name</p>
          <input v-model="selectedEmployeeData.firstName" />
        </div>
        <div class="details-label">
          <p class="label">Last name</p>
          <input v-model="selectedEmployeeData.lastName" />
        </div>
        <div class="details-label">
          <p class="label">Job title</p>
          <input v-model="selectedEmployeeData.title" />
        </div>
        <div class="details-label">
          <p class="label">Job function</p>
          <input v-model="selectedEmployeeData.jobFunction" />
        </div>
        <div class="details-label">
          <p class="label">Email</p>
          <input v-model="selectedEmployeeData.email" />
        </div>
        <div class="details-label">
          <p class="label">Phone number</p>
          <input v-model="selectedEmployeeData.phoneNumber" />
        </div>
      </div>
      <button @click="saveEmployeeDetails">Save</button>
    </div>

    <div class="body">
      <div class="header">
        <input
          type="text"
          v-model="searchName"
          class="searchbar"
          placeholder="Search"
          @input="searchEmployee"
        />
        <button @click="addEmployee">Add employee</button>
      </div>
      <div v-if="employeeData && employeeData.length !== 0">
        <h1>List of employees</h1>
        <div class="scrollable-mobile">
          <div class="table-header">
            <div></div>
            <div>First name</div>
            <div>Last name</div>
            <div>Job title</div>
            <div>Job function</div>
            <div></div>
          </div>
          <div
            v-for="(employee, index) in employeeData"
            :key="index"
            class="employee-row"
            @click="openEmployeeDetails(employee, index, $event)"
          >
            <div class="semi-bold">{{ index + 1 }}</div>
            <div>{{ employee.firstName }}</div>
            <div>{{ employee.lastName }}</div>
            <div>{{ employee.title }}</div>
            <div>{{ employee.jobFunction }}</div>
            <div class="remove-employee">
              <img
                src="../assets/trashcan-iconpng.png"
                alt="Trash can icon"
                class="remove-icon"
              />
            </div>
          </div>
        </div>
      </div>
      <div v-else>
        <h2>No employees are found</h2>
      </div>
    </div>
  </div>
</template>
  
  <script>
export default {
  data() {
    return {
      newEmployeeFirstName: "",
      newEmployeeLastName: "",
      newEmployeeTitle: "",
      newEmployeeJobFunction: "",
      newEmployeeEmail: "",
      newEmployeePhoneNumber: "",
      searchName: "",
      showOverlay: false,
      showAddEmployeeModal: false,
      showRemoveEmployeeModal: false,
      showEmployeeDetailsModal: false,
      employeeData: [],
      selectedEmployee: "",
      selectedEmployeeData: {},
      showEditModal: false,
      selectedEmployeeIndex: 0,
    };
  },
  mounted() {
    var retrievedObject =
    JSON.parse(localStorage.getItem("employeeData")) !== ""
        ? JSON.parse(localStorage.getItem("employeeData"))
        : localStorage.removeItem("employeeData");
    if (retrievedObject) {
      this.employeeData = retrievedObject;
    }
  },
  methods: {
    addEmployee() {
      this.showAddEmployeeModal = true;
      this.showOverlay = true;
    },
    AddNewEmployee() {
      this.employeeData == null
        ? (this.employeeData = [
            {
              firstName: this.newEmployeeFirstName,
              lastName: this.newEmployeeLastName,
              title: this.newEmployeeTitle,
              jobFunction: this.newEmployeeJobFunction,
              email: this.newEmployeeEmail,
              phoneNumber: this.newEmployeePhoneNumber,
            },
          ])
        : this.employeeData.push({
            firstName: this.newEmployeeFirstName,
            lastName: this.newEmployeeLastName,
            title: this.newEmployeeTitle,
            jobFunction: this.newEmployeeJobFunction,
            email: this.newEmployeeEmail,
            phoneNumber: this.newEmployeePhoneNumber,
          }),
        window.localStorage.setItem(
          "employeeData",
          JSON.stringify(this.employeeData)
        );
      this.showOverlay = false;
      this.showAddEmployeeModal = false;

      setTimeout(() => {
        this.newEmployeeFirstName = "";
        this.newEmployeeLastName = "";
        this.newEmployeeTitle = "";
        this.newEmployeeJobFunction = "";
        this.newEmployeeEmail = "";
        this.newEmployeePhoneNumber = "";
      }, 200);
    },
    openRemoveEmployeeModal(index) {
      this.selectedEmployeeIndex = index;
      this.showOverlay = true;
      this.showRemoveEmployeeModal = true;
    },
    closeRemoveEmployeeModal() {
      this.showOverlay = false;
      this.showRemoveEmployeeModal = false;
    },
    removeEmployee() {
      this.employeeData.splice(this.selectedEmployeeIndex, 1);
      window.localStorage.setItem("employeeData", JSON.stringify(this.employeeData));
      this.closeRemoveEmployeeModal();
    },
    closeOverlay() {
      this.showOverlay = false;
      if (this.showAddEmployeeModal) {
        this.showAddEmployeeModal = false;
      }
      if (this.showRemoveEmployeeModal) {
        this.showRemoveEmployeeModal = false;
      }
      if (this.showEmployeeDetailsModal) {
        this.showEmployeeDetailsModal = false;
      }
      if (this.showEditModal) {
        this.showEditModal = false;
      }
    },
    openEmployeeDetails(employee, index, event) {
      if (event.target.className === "remove-employee" || event.target.className === "remove-icon") {
        this.openRemoveEmployeeModal(index);
      } else {
        this.selectedEmployeeIndex = index;
        this.showOverlay = true;
        this.showEmployeeDetailsModal = true;
        this.selectedEmployee = `${employee.firstName} ${employee.lastName}`;
        this.selectedEmployeeData = this.employeeData[index];
      }
    },
    searchEmployee() {
      this.searchName !== ""
        ? (
          this.employeeData = JSON.parse(window.localStorage.getItem("employeeData")).filter(
            (x) =>
              x.firstName
                .toLowerCase()
                .includes(this.searchName.toLowerCase()) ||
              x.lastName.toLowerCase().includes(this.searchName.toLowerCase())
          ))
        : (this.employeeData = JSON.parse(
            window.localStorage.getItem("employeeData")
          ));
    },
    saveEmployeeDetails() {
      this.employeeData[this.selectedEmployeeIndex] = this.selectedEmployeeData;
      window.localStorage.setItem(
        "employeeData",
        JSON.stringify(this.employeeData)
      );

      this.closeOverlay();
    },
  },
};
</script>
  
<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,300;0,400;0,500;0,600;0,700;0,800;1,300;1,400;1,500;1,600;1,700;1,800&display=swap");

//helpers

.semi-bold {
  font-weight: 600 !important;
}

// end of helpers
html,
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
}

div {
  font-family: "Open Sans", sans-serif;
}

button {
  height: 48px;
  width: 200px;
  font-weight: 600;
  font-size: 18px;
  padding: 8px 16px;
  border-radius: 16px;
  background: darkcyan;
  border: none;
  color: white;
  box-shadow: 0 1px 3px rgba(119, 119, 119, 0.8);
  cursor: pointer;

  &:hover {
    background: #016a4b;
  }

  &.no-btn {
    background: darkred;
    margin-left: 8px;

    &:hover {
      background: red;
    }
  }
}
.bg-overlay {
  z-index: 100;
  position: fixed;
  background-color: #212121;
  opacity: 0;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  transition: 0s;
  pointer-events: none;
  &.active {
    opacity: 0.97;
    pointer-events: all;
    transition: 0.5s;
  }
}

h1 {
  font-size: 50px;

  @media only screen and (max-width: 1024px) {
    font-size: 30px;
  }
}

.modal {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 55%;
  transform: translate(-50%, -50%);
  z-index: 110;
  background: white;
  padding: 24px 48px;
  text-align: center;
  border-radius: 16px;

  @media only screen and (max-width: 1024px) {
    padding: 24px;
    width: 75%;
  }

  h2 {
    margin: 0;
    margin: 24px 0 16px 0;
    font-size: 32px;
    font-weight: 400;

    @media only screen and (max-width: 1024px) {
      font-size: 24px;
    }
  }

  .close-img {
    position: absolute;
    right: 24px;
    top: 24px;
    cursor: pointer;

    @media only screen and (max-width: 1024px) {
      right: 16px;
      top: 16px;
    }

    &:hover {
      filter: invert(1);
    }
  }

  button {
    margin-top: 16px;
  }
}
.add-employee-modal {
  .input-fields {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: center;

    @media only screen and (max-width: 1024px) {
      flex-direction: column;
    justify-content: space-between;
    }

    .label-input {
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      margin-bottom: 8px;
      margin-right: 8px;

      &:last-child {
        margin-bottom: 0;
      }

      label {
        font-size: 18px;
        font-weight: 300;
      }
      input {
        width: 250px;
        height: 24px;
        margin-top: 4px;
        margin-right: 8px;
        border-radius: 12px;
        border: none;
        box-shadow: 0 1px 3px rgba(119, 119, 119, 0.8);
      padding: 4px 8px;

        @media only screen and (max-width: 1024px) {
          width: 100%;
        }
      }
    }
  }
}

.employee-details-modal,
.edit-employee-modal {
  .employee-datails {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: center;

    .details-label {
      display: flex;
      flex-direction: column;
      margin-top: 16px;
      width: calc(100%/3);
      margin-right: 80px;

      @media only screen and (max-width: 1024px) {
        width: 100% !important;
        margin-right: 0 !important;
      }

      @media only screen and (min-width: 1024px) {
        &:nth-child(2n) {
          margin-right: 0;
        }
      }

      .label {
        font-size: 16px;
        color: gray;

        @media only screen and (max-width: 1024px) {
          font-size: 14px;
        }
      }

      p,
      a {
        margin: 0;
        font-size: 26px;
        color: black;
        text-decoration: none;

        @media only screen and (max-width: 1024px) {
          font-size: 18px;
        }
      }
    }
  }
}

.edit-employee-modal {
  .details-label {
    align-items: center;
    margin-right: 0 !important;
  }
  .details-label {
    width: 48% !important;

    input {
      width: 250px;
      height: 24px;
      margin-top: 4px;
      border-radius: 12px;
      border: none;
      box-shadow: 0 1px 3px rgba(119, 119, 119, 0.8);
      padding: 4px 8px;
      font-size: 16px;

      @media only screen and (max-width: 1024px) {
        width: 80% !important;
      }
    }
  }
}

.body {
  max-width: 1140px;
  margin: 80px auto;
  text-align: center;

  @media only screen and (max-width: 1024px) {
    margin: 32px 0;
  }

  .header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;

    @media only screen and (max-width: 1024px) {
      flex-direction: column;
      margin: 0 16px;
    }

    .searchbar {
      height: 48px;
      width: 500px;
      padding: 4px 24px;
      border-radius: 32px;
      border: none;
      box-shadow: 0 1px 3px rgba(119, 119, 119, 0.8);
      font-size: 18px;

      @media only screen and (max-width: 1024px) {
        width: 90%;
        margin-bottom: 16px;
      }
    }
  }

  .scrollable-mobile {
    @media only screen and (max-width: 1024px) {
      overflow-x: scroll;
      display: flex;
      flex-direction: column;
      white-space: nowrap;
      padding: 0 16px;
      -ms-overflow-style: none;
      scrollbar-width: none;

      &::-webkit-scrollbar {
        display: none;
      }
    }
  }

  .table-header,
  .employee-row {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    border: 1px solid black;

    @media only screen and (max-width: 1024px) {
      width: fit-content;
    }

    &:hover {
      background: #f2f2f2;
    }

    div {
      width: calc(100% / 4);
      padding: 4px 8px;
      border-left: 1px solid black;
      height: 32px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-weight: 700;

      @media only screen and (max-width: 1024px) {
        width: 100%;
        min-width: 150px;
      }

      &:first-child {
        width: 5%;
        border-left: none;
        background: lightgray;

        @media only screen and (max-width: 1024px) {
          width: 10%;
          min-width: 35px;
        }
      }
      &:last-child {
        width: 5%;
        background: lightgray;

        @media only screen and (max-width: 1024px) {
          width: 10%;
          min-width: 35px;
        }
      }
    }
  }

  .employee-row {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    border-top: none;
    border-bottom: 1px solid black;
    cursor: pointer;

    div:last-child {
      background: transparent;

      &:hover {
        background: darkred;
        img {
          filter: invert(1);
        }
      }
    }

    .remove-icon {
      width: 20px;
      height: 26px;
    }

    div {
      font-weight: 400;
    }
  }
}
</style>
  