<template>
  <div>
    <navigation-page v-if="tenant" icon="fa-map" :title="tenant.name"
      loadingMessage="Loading tenant configuration ..." :loaded="loaded">
      <div slot="content">
        <tenant-detail-header :tenant="tenant">
        </tenant-detail-header>
        <v-tabs v-model="active">
          <v-tabs-bar dark color="primary">
            <v-tabs-item key="microservices" href="#microservices">
              Microservices
            </v-tabs-item>
            <v-tabs-slider></v-tabs-slider>
          </v-tabs-bar>
          <v-tabs-items>
            <v-tabs-content key="microservices" id="microservices">
              <microservice-list :topology="tenantTopology"
                @microserviceClicked="onMicroserviceClicked">
              </microservice-list>
            </v-tabs-content>
          </v-tabs-items>
        </v-tabs>
      </div>
      <div slot="actions">
        <navigation-action-button icon="edit" tooltip="Edit Tenant"
          @action="onEdit">
        </navigation-action-button>
        <navigation-action-button icon="times" tooltip="Delete Tenant"
          @action="onDelete">
        </navigation-action-button>
      </div>
    </navigation-page>
    <tenant-update-dialog ref="edit" :tenantToken="tenantToken"
      @tenantUpdated="onTenantEdited">
    </tenant-update-dialog>
    <tenant-delete-dialog ref="delete" :tenantToken="tenantToken"
      @tenantDeleted="onTenantDeleted">
    </tenant-delete-dialog>
  </div>
</template>

<script>
import NavigationPage from "../common/NavigationPage";
import NavigationActionButton from "../common/NavigationActionButton";
import FloatingActionButton from "../common/FloatingActionButton";
import TenantDetailHeader from "./TenantDetailHeader";
import TenantUpdateDialog from "./TenantUpdateDialog";
import TenantDeleteDialog from "./TenantDeleteDialog";
import MicroserviceList from "../microservice/MicroserviceList";
import {
  _getTenant,
  _getTenantTopology
} from "../../http/sitewhere-api-wrapper";

export default {
  data: () => ({
    tenantToken: null,
    tenant: null,
    tenantTopology: null,
    active: null,
    loaded: false
  }),

  components: {
    NavigationPage,
    NavigationActionButton,
    FloatingActionButton,
    TenantDetailHeader,
    TenantUpdateDialog,
    TenantDeleteDialog,
    MicroserviceList
  },

  created: function() {
    this.$data.tenantToken = this.$route.params.tenantToken;
    this.refresh();
  },

  methods: {
    // Called if a microservice is clicked.
    onMicroserviceClicked: function(microservice) {
      this.$router.push(
        "/system/tenants/" +
          this.$data.tenantToken +
          "/" +
          microservice.identifier
      );
    },

    // Called to refresh data.
    refresh: function() {
      // Load tenant data.
      this.refreshTenant();

      // Load configuration data.
      var component = this;
      _getTenantTopology(this.$store)
        .then(function(response) {
          component.$data.tenantTopology = response.data;
        })
        .catch(function(e) {});
    },

    // Refresh only tenant information.
    refreshTenant: function() {
      this.$data.loaded = false;
      var component = this;
      _getTenant(this.$store, this.$data.tenantToken, true)
        .then(function(response) {
          component.loaded = true;
          component.onLoaded(response.data);
        })
        .catch(function(e) {
          component.loaded = true;
        });
    },

    // Called after data is loaded.
    onLoaded: function(tenant) {
      this.$data.tenant = tenant;
      var section = {
        id: "tenants",
        title: "Manage Tenant",
        icon: "layer-group",
        route: "/tenants/" + tenant.token,
        longTitle: "Manage Tenant: " + tenant.token
      };
      this.$store.commit("currentSection", section);
    },
    // Called to edit tenant.
    onEdit: function() {
      this.$refs["edit"].onOpenDialog();
    },
    // Called after tenant is edited.
    onTenantEdited: function() {
      this.$emit("refresh");
    },
    // Called to delete tenant.
    onDelete: function() {
      this.$refs["delete"].showDeleteDialog();
    },
    // Called after tenant is deleted.
    onTenantDeleted: function() {
      this.$router.push("/system/tenants");
    }
  }
};
</script>

<style>
</style>
