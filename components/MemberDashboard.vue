<template>
    <UCard class="max-h-[80vh] overflow-scroll">
        <template #header>
            <div class="flex justify-between items-center gap-2">
                <div class="flex flex-col">
                    <span class="font-medium">{{ memberName }} · Summary</span>
                    <span class="text-xs opacity-70">In this group</span>
                </div>

                <UButton @click="$emit('close')" variant="ghost" color="gray" icon="i-heroicons-x-mark" />
            </div>
        </template>

        <div class="space-y-4">
            <!-- Overall net (all time, in this group) -->
            <div class="text-sm">
                <span v-if="netAllTime === 0">You are settled up.</span>

                <span v-else-if="netAllTime > 0">
                    {{ memberName }} owes you
                    <span class="color-positive font-medium">
                        {{ currency }}{{ netAllTime }}
                    </span>
                </span>

                <span v-else>
                    You owe {{ memberName }}
                    <span class="color-negative font-medium">
                        {{ currency }}{{ Math.abs(netAllTime) }}
                    </span>
                </span>
            </div>

            <!-- NEW: Monthly + Yearly summaries -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
                <!-- This Month -->
                <UCard>
                    <div class="flex items-start justify-between">
                        <div class="flex flex-col">
                            <span class="font-medium">This month</span>
                            <span class="text-xs opacity-60">{{ monthLabel }}</span>
                        </div>

                        <div class="text-sm">
                            <span v-if="netMonth === 0" class="opacity-70">Settled</span>
                            <span v-else-if="netMonth > 0" class="color-positive font-medium">
                                +{{ currency }}{{ netMonth }}
                            </span>
                            <span v-else class="color-negative font-medium">
                                -{{ currency }}{{ Math.abs(netMonth) }}
                            </span>
                        </div>
                    </div>

                    <UDivider class="my-2" />

                    <div class="text-sm space-y-1">
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">You paid (expenses)</span>
                            <span>{{ currency }}{{ monthSummary.youPaidExpenses }}</span>
                        </div>
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">{{ memberName }} paid (expenses)</span>
                            <span>{{ currency }}{{ monthSummary.theyPaidExpenses }}</span>
                        </div>

                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">Your share</span>
                            <span>{{ currency }}{{ monthSummary.yourShare }}</span>
                        </div>
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">{{ memberName }}’s share</span>
                            <span>{{ currency }}{{ monthSummary.theirShare }}</span>
                        </div>

                        <UDivider class="my-2" />

                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">You paid {{ memberName }}</span>
                            <span>{{ currency }}{{ monthSummary.youPaidThem }}</span>
                        </div>
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">{{ memberName }} paid you</span>
                            <span>{{ currency }}{{ monthSummary.theyPaidYou }}</span>
                        </div>
                    </div>
                </UCard>

                <!-- This Year -->
                <UCard>
                    <div class="flex items-start justify-between">
                        <div class="flex flex-col">
                            <span class="font-medium">This year</span>
                            <span class="text-xs opacity-60">{{ yearLabel }}</span>
                        </div>

                        <div class="text-sm">
                            <span v-if="netYear === 0" class="opacity-70">Settled</span>
                            <span v-else-if="netYear > 0" class="color-positive font-medium">
                                +{{ currency }}{{ netYear }}
                            </span>
                            <span v-else class="color-negative font-medium">
                                -{{ currency }}{{ Math.abs(netYear) }}
                            </span>
                        </div>
                    </div>

                    <UDivider class="my-2" />

                    <div class="text-sm space-y-1">
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">You paid (expenses)</span>
                            <span>{{ currency }}{{ yearSummary.youPaidExpenses }}</span>
                        </div>
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">{{ memberName }} paid (expenses)</span>
                            <span>{{ currency }}{{ yearSummary.theyPaidExpenses }}</span>
                        </div>

                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">Your share</span>
                            <span>{{ currency }}{{ yearSummary.yourShare }}</span>
                        </div>
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">{{ memberName }}’s share</span>
                            <span>{{ currency }}{{ yearSummary.theirShare }}</span>
                        </div>

                        <UDivider class="my-2" />

                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">You paid {{ memberName }}</span>
                            <span>{{ currency }}{{ yearSummary.youPaidThem }}</span>
                        </div>
                        <div class="flex justify-between gap-3">
                            <span class="opacity-70">{{ memberName }} paid you</span>
                            <span>{{ currency }}{{ yearSummary.theyPaidYou }}</span>
                        </div>
                    </div>
                </UCard>
            </div>

            <UDivider />

            <!-- Transaction history (already) -->
            <div class="space-y-2">
                <div class="flex items-center justify-between">
                    <span class="font-medium">Transactions with {{ memberName }}</span>
                    <span class="text-xs opacity-60">{{ flatTransactions.length }} items</span>
                </div>

                <div v-if="grouped.length === 0" class="text-sm opacity-70">
                    No transactions found for this member in this group.
                </div>

                <div v-else class="space-y-2">
                    <div v-for="g in grouped" :key="g.month">
                        <span class="text-lg">{{ g.month }}</span>
                        <ExpenseItem v-for="tx in g.transactions" :key="tx.id" :expense="tx" @edit="noop" />
                    </div>
                </div>
            </div>
        </div>
    </UCard>
</template>

<script setup>
import moment from "moment";
import { round, groupGetPayments } from "~/composables/useGroups.client";

const props = defineProps({
    memberId: { type: String, required: true },
});
defineEmits(["close"]);

const groupID = useGroupID();
const { getGroupByID } = storeToRefs(useGroups());

const group = computed(() => getGroupByID.value(groupID));
const currency = computed(() => useGroups().getGroupCurrency(groupID));
const myID = computed(() => group.value?.myID);

const memberName = computed(() =>
    useGroups().getMemberName(groupID, props.memberId, true),
);

function involves(tx, id) {
    return Boolean(tx?.payers?.[id] != null || tx?.splitters?.[id] != null);
}

const flatTransactions = computed(() => {
    const g = group.value;
    if (!g) return [];
    const order = [...(g.transactionOrder || [])].reverse();
    return order
        .map((id) => g.transactions?.[id])
        .filter(Boolean)
        .filter((tx) => involves(tx, props.memberId));
});

const grouped = computed(() => {
    const out = [];
    let current = null;

    for (const tx of flatTransactions.value) {
        const month = moment(tx.created_at).format("MMMM YYYY");
        if (current?.month === month) current.transactions.push(tx);
        else {
            current = { month, transactions: [tx] };
            out.push(current);
        }
    }
    return out;
});

function getPeriodTx(start, end) {
    return flatTransactions.value.filter((tx) => {
        const t = moment(tx.created_at);
        return t.isSameOrAfter(start) && t.isSameOrBefore(end);
    });
}

function getNetForPeriodTx(periodTx) {
    const g = group.value;
    if (!g) return 0;

    // Build a pseudo-group with ONLY the period transactions,
    // then reuse PeerSplit's settlement algorithm.
    const txMap = {};
    for (const tx of periodTx) txMap[tx.id] = tx;

    const pseudoGroup = { members: g.members, transactions: txMap };
    const payments = groupGetPayments(pseudoGroup);

    let net = 0;
    for (const p of payments) {
        if (p.from === props.memberId && p.to === myID.value) net += Number(p.value);
        if (p.from === myID.value && p.to === props.memberId) net -= Number(p.value);
    }
    return round(net);
}

function summarize(periodTx) {
    let youPaidExpenses = 0;
    let theyPaidExpenses = 0;
    let yourShare = 0;
    let theirShare = 0;

    let youPaidThem = 0; // payments you made to them
    let theyPaidYou = 0; // payments they made to you

    for (const tx of periodTx) {
        const c = computeTransaction(tx);

        if (c.type === "expense") {
            youPaidExpenses = round(youPaidExpenses + Number(c.payers?.[myID.value] || 0));
            theyPaidExpenses = round(theyPaidExpenses + Number(c.payers?.[props.memberId] || 0));

            yourShare = round(yourShare + Number(c.splits?.[myID.value] || 0));
            theirShare = round(theirShare + Number(c.splits?.[props.memberId] || 0));
        }

        if (c.type === "payment") {
            // payer = sender, splitter = receiver in this codebase
            const sender = Object.keys(c.payers || {})[0];
            const receiver = Object.keys(c.splitters || {})[0];
            const amt = Number(c.totalCost || 0);

            if (sender === myID.value && receiver === props.memberId) {
                youPaidThem = round(youPaidThem + amt);
            }
            if (sender === props.memberId && receiver === myID.value) {
                theyPaidYou = round(theyPaidYou + amt);
            }
        }
    }

    return {
        youPaidExpenses,
        theyPaidExpenses,
        yourShare,
        theirShare,
        youPaidThem,
        theyPaidYou,
    };
}

// Month + Year periods (current)
const monthStart = computed(() => moment().startOf("month"));
const monthEnd = computed(() => moment().endOf("day"));
const yearStart = computed(() => moment().startOf("year"));
const yearEnd = computed(() => moment().endOf("day"));

const monthLabel = computed(() => moment().format("MMMM YYYY"));
const yearLabel = computed(() => moment().format("YYYY"));

const txMonth = computed(() => getPeriodTx(monthStart.value, monthEnd.value));
const txYear = computed(() => getPeriodTx(yearStart.value, yearEnd.value));

const netMonth = computed(() => getNetForPeriodTx(txMonth.value));
const netYear = computed(() => getNetForPeriodTx(txYear.value));
const netAllTime = computed(() => getNetForPeriodTx(flatTransactions.value));

const monthSummary = computed(() => summarize(txMonth.value));
const yearSummary = computed(() => summarize(txYear.value));

function noop() { }
</script>
