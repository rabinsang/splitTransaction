<template>
  <div class="px-1">
    <UCard>
      <div class="flex flex-col gap-1">
        <span v-if="balance === 0" class="flex items-center gap-2">
          You are settled up in this group.
        </span>

        <div v-else>
          <span class="flex items-center gap-2">
            You {{ balance > 0 ? "are owed" : "owe" }}
            <span :class="[
              'text-2xl',
              balance > 0 ? 'color-positive' : 'color-negative',
            ]">
              {{ useGroups().getGroupCurrency(groupID) }}{{ Math.abs(balance) }}
            </span>
          </span>

          <!-- List of simplified settlement lines -->
          <div>
            <span v-for="payment in payments" :key="`${payment.from}-${payment.to}-${payment.value}`"
              class="flex items-center gap-1 text-sm">
              -&nbsp;

              <!-- FROM name (clickable if it's not you) -->
              <button v-if="payment.from !== myID" type="button" class="underline underline-offset-2 hover:opacity-80"
                @click.stop="openMember(payment.from)">
                {{ useGroups().getMemberName(groupID, payment.from) }}
              </button>
              <span v-else>
                {{ useGroups().getMemberName(groupID, payment.from) }}
              </span>

              {{ payment.from === myID ? "owe" : "owes" }}

              <!-- TO name (clickable if it's not you) -->
              <button v-if="payment.to !== myID" type="button" class="underline underline-offset-2 hover:opacity-80"
                @click.stop="openMember(payment.to)">
                {{ useGroups().getMemberName(groupID, payment.to, true) }}
              </button>
              <span v-else>
                {{ useGroups().getMemberName(groupID, payment.to, true) }}
              </span>

              <span :class="[
                'text-md',
                balance > 0 ? 'color-positive' : 'color-negative',
              ]">
                {{ useGroups().getGroupCurrency(groupID) }}{{ payment.value }}
              </span>
            </span>
          </div>
        </div>
      </div>
    </UCard>

    <!-- Member dashboard modal -->
    <UModal v-model="showMemberDashboard">
      <MemberDashboard v-if="selectedMemberId" :member-id="selectedMemberId" @close="showMemberDashboard = false" />
    </UModal>
  </div>
</template>

<script setup>
const { groupID } = defineProps(["groupID"]);
const { getBalancesByGroupID, getGroupByID, getPaymentsByGroupID } =
  storeToRefs(useGroups());

const myID = computed(() => {
  const { myID } = getGroupByID.value(groupID);
  return myID;
});

const balance = computed(() => {
  const balances = getBalancesByGroupID.value(groupID);
  return balances[myID.value] || 0;
});

const payments = computed(() => {
  return getPaymentsByGroupID
    .value(groupID)
    .filter((payment) => payment.from === myID.value || payment.to === myID.value);
});

/* Member dashboard modal state */
const showMemberDashboard = ref(false);
const selectedMemberId = ref(null);

function openMember(id) {
  if (!id || id === myID.value) return;
  selectedMemberId.value = id;
  showMemberDashboard.value = true;
}
</script>
