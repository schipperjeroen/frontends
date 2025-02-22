<script setup lang="ts">
import { CustomerAddress, Country, Salutation } from "@shopware-pwa/types";
import { SharedModal } from "~~/components/shared/SharedModal.vue";
const { pushSuccess, pushError } = useNotifications();
const { setDefaultCustomerShippingAddress, setDefaultCustomerBillingAddress } =
  useAddress();
const { defaultBillingAddressId, defaultShippingAddressId } = useUser();
const { refreshSessionContext } = useSessionContext();
const modal = inject<SharedModal>("modal") as SharedModal;

const props = withDefaults(
  defineProps<{
    address: CustomerAddress;
    countries: Array<Country>;
    salutations: Array<Salutation>;
    canSetDefault?: boolean;
    canEdit?: boolean;
  }>(),
  {
    canSetDefault: true,
    canEdit: true,
  }
);

const setDefaultShippingAddress = async () => {
  try {
    await setDefaultCustomerShippingAddress(props.address.id);
    refreshSessionContext();
    pushSuccess("Set default shipping address successfully");
  } catch (error) {
    pushError("Set default shipping address error");
  }
};

const setDefaultBillingAddress = async () => {
  try {
    await setDefaultCustomerBillingAddress(props.address.id);
    refreshSessionContext();
    pushSuccess("Set default billing address successfully");
  } catch (error) {
    pushError("Set default billing address error");
  }
};
</script>

<template>
  <div class="col-span-6 lg:col-span-3 max-w-md">
    <div class="flex items-center mb-2">
      <h5 class="text-xl leading-none text-gray-900 mr-2">
        {{ `${address.firstName} ${address.lastName}` }}
      </h5>
      <div
        v-if="canEdit"
        i-carbon-edit
        text-xl
        inline-block
        @click.prevent="
          modal.open('AccountAddressForm', {
            address,
            salutations,
            countries,
            title: 'Edit address',
          })
        "
      />
    </div>
    <div class="flow-root">
      <span class="block">{{ address.street }}</span>
      <span class="block">{{ address.zipcode }}</span>
      <span class="block">{{ address.city }}</span>
    </div>
    <div v-if="canSetDefault">
      <a
        v-if="defaultShippingAddressId !== address.id"
        role="button"
        tabindex="0"
        class="block text-sm mt-4 font-medium text-blue-600 hover:underline"
        @click="setDefaultShippingAddress()"
      >
        Set as default shipping address
      </a>
      <a
        v-if="defaultBillingAddressId !== address.id"
        role="button"
        tabindex="0"
        class="block text-sm mt-2 font-medium text-blue-600 hover:underline"
        @click="setDefaultBillingAddress()"
      >
        Set as default billing address
      </a>
    </div>
  </div>
</template>
