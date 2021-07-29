<template>
  <div>
    <ul
      class="list-group"
      v-for="(permissionGroup, permissionGroupIndex) in tokenPermissions"
      :key="`${permissionGroup.groupName}_${permissionGroupIndex}`"
    >
      <li class="list-group-item">
        <div class="permission-container">
          <div>
            <input
              type="checkbox"
              :indeterminate.prop="
                !permissionGroup.allSelected && permissionGroup.someSelected
              "
              v-model="permissionGroup.allSelected"
              :id="permissionGroup.groupName"
              v-on:change="
                permissionGroupCheckboxChanged($event, permissionGroupIndex)
              "
            />
            <label :for="permissionGroup.groupName" class="cursor-pointer"
              >{{ permissionGroup.groupName }} -
              <span style="color: red; margin-left: 14px; padding-right: 3px">{{
                permissionGroup.allSelected
              }}</span></label
            >
          </div>
          <div class="permission-summary">
            {{ permissionGroup.summary }}
          </div>
        </div>
        <ul class="list-group">
          <li
            class="list-group-item list-group-item-no-margin"
            v-for="(permission, permissionIndex) in permissionGroup.permissions"
            :key="`${permissionGroup.groupName}_${permission.name}_${permissionIndex}`"
          >
            <div class="permission-container">
              <div>
                <input
                  type="checkbox"
                  :id="permission.name"
                  v-bind:checked="permission.selected"
                  v-on:change="
                    permissionGroupCheckboxChanged(
                      $event,
                      permissionGroupIndex,
                      permissionIndex
                    )
                  "
                />
                <label :for="permission.name" class="cursor-pointer"
                  >{{ permission.name
                  }}<span
                    style="color: red; margin-left: 3px; padding-right: 3px"
                    >&nbsp; {{ permission.selected }}</span
                  ></label
                >
              </div>
              <div class="permission-summary">
                {{ permission.summary }}
              </div>
            </div>
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      tokenPermissions: [
        {
          groupName: "App",
          allSelected: false,
          someSelected: false,
          summary: "Full access to all project operations",
          permissions: [
            {
              name: "can_create_app",
              summary: "Create new projects",
              selected: false,
            },
            {
              name: "can_delete_app",
              summary: "Delete existing projects",
              selected: false,
            },
            {
              name: "can_edit_app",
              summary: "Edit an existing project",
              selected: false,
            },
          ],
        }
      ],
    };
  },
  methods: {
    getPermissionGroupSelectionStatus: function (permissionGroup) {
      let allSelected = true;
      let someSelected = false;

      permissionGroup.permissions.forEach((permission) => {
        if (permission.selected === false) {
          allSelected = false;
        }
        if (permission.selected === true) {
          someSelected = true;
        }
      });

      return { allSelected, someSelected };
    },
    permissionGroupCheckboxChanged: function (
      $event,
      permissionGroupIndex,
      permissionIndex
    ) {
      const { checked } = $event.target;

      // its single permission selected
      if (permissionIndex !== undefined) {
        this.tokenPermissions[permissionGroupIndex].permissions[
          permissionIndex
        ].selected = checked;

        const { allSelected, someSelected } =
          this.getPermissionGroupSelectionStatus(
            this.tokenPermissions[permissionGroupIndex]
          );

        this.tokenPermissions[permissionGroupIndex].allSelected = allSelected;
        this.tokenPermissions[permissionGroupIndex].someSelected = someSelected;
      } else {
        // its selectAll check box
        const { allSelected, someSelected } =
          this.getPermissionGroupSelectionStatus(
            this.tokenPermissions[permissionGroupIndex]
          );

        let checkAll;

        // no checkbox / permission is selected then set all
        if (!someSelected && !allSelected) {
          checkAll = true;
        } else {
          checkAll = false;
        }

        this.tokenPermissions[permissionGroupIndex].allSelected = checkAll;
        this.tokenPermissions[permissionGroupIndex].someSelected = checkAll;

        for (
          let i = 0;
          i < this.tokenPermissions[permissionGroupIndex].permissions.length;
          i++
        ) {
          this.tokenPermissions[permissionGroupIndex].permissions[i].selected =
            checkAll;
        }
      }
    },
  },
};
</script>

<style scoped>
.circle {
  -moz-border-radius: 80px/80px;
  -webkit-border-radius: 80px 80px;
  border-radius: 80px/80px;
  width: 80px;
  height: 80px;
  font-size: 55px;
  font-weight: 100;
  border: solid 3px #a5dc86;
  color: #a5dc86;
  text-align: center;
  vertical-align: middle;
  padding: 10px;
  display: inline-block;
}

.list-group-item-no-margin {
  border: 0;
}

.list-group-item:hover {
  background-color: initial;
}

.permission-container {
  display: flex;
  flex-wrap: wrap;
}

.checkbox-wrapper {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  align-content: center;
  padding-right: 5px;
  width: 180px;
}

.checkbox-wrapper input {
  margin-right: 4px;
  margin-top: 0;
}

.permission-summary {
  color: #8b949e;
  min-width: 240px;
}

label {
  margin: 0;
  color: #000000;
}
</style>
