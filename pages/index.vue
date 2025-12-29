<template>
  <div class="min-h-screen flex flex-col">
    <div class="mx-auto container max-w-[800px] px-8 pb-20 flex-grow space-y-8">
      <!-- Hero -->
      <div class="text-center space-y-6 my-10">
        <div class="flex flex-col items-center gap-3">
          <Icon class="w-20 h-20" filled :fontControlled="false" />
          <h1 class="text-6xl select-none">
            <span class="text-primary">Split</span><span class="text-lime-300">Transaction</span>
          </h1>
          <h3 class="text-2xl">
            Personal Splitwise-style tracker. Offline-first. Data stays on this device.
          </h3>
        </div>

        <div class="flex flex-row flex-wrap items-center justify-center gap-2">
          <UButton to="/app" size="xl">Open App</UButton>
          <UButton variant="outline" to="https://github.com/rabinsang/splitTransaction" size="xl" target="_blank">
            View on GitHub
          </UButton>
        </div>

        <UAlert icon="i-heroicons-lock-closed" color="primary" variant="subtle" title="Local-only by default"
          description="This app stores data locally on the device you use (phone or laptop). It does not automatically sync across devices unless you choose to export/import." />
      </div>

      <!-- Short description -->
      <div class="text-xl space-y-4">
        <p>
          <span class="text-primary font-medium">SplitTransaction</span> is a personal
          expense tracker inspired by Splitwise. It helps you record group expenses,
          see who owes who, and review transactions clearly.
        </p>
        <p>
          Designed for <span class="font-semibold">personal use</span>: you can run it as a website,
          install it as a PWA (app-like), and keep everything
          <span class="font-semibold">offline and private</span>.
        </p>
        <p class="opacity-80">
          Tip: export CSV occasionally as a backup if you care about long-term history.
        </p>
      </div>

      <!-- Features -->
      <h2 class="text-3xl font-medium text-primary-400 underline">FEATURES</h2>
      <div class="text-lg space-y-1">
        <p v-for="feature in features" :key="feature.text">
          {{ feature.text }}
        </p>
      </div>

      <!-- Quick Start -->
      <div class="space-y-6">
        <h2 class="text-3xl font-medium text-primary-400 underline">
          QUICK START GUIDE
        </h2>

        <div class="text-lg space-y-4">
          <p>
            Use it directly in your browser, or install it as a PWA for an app-like
            experience. The data will stay on <span class="font-semibold">that device</span>.
          </p>

          <UAccordion :items="[
            { label: 'On Android (Chrome)' },
            { label: 'On iPhone/iPad (Safari)' },
            { label: 'On Desktop (Chrome / Edge)' },
          ]" :ui="{
              item: { color: 'text-gray-800 dark:text-gray-100' },
            }">
            <template #item="{ item }">
              <!-- Android -->
              <div v-if="item.label === 'On Android (Chrome)'" class="text-lg space-y-4">
                <ol class="list-decimal pl-5 space-y-2">
                  <li>Open <span class="font-semibold">Chrome</span> and visit the app URL.</li>
                  <li>Tap the menu (three dots).</li>
                  <li>Select <span class="font-semibold">Install app</span> or <span class="font-semibold">Add to Home
                      screen</span>.</li>
                  <li>Open it from your home screen like a normal app.</li>
                  <li class="opacity-80">Your data stays on your phone unless you export/import.</li>
                </ol>
              </div>

              <!-- iOS -->
              <div v-else-if="item.label === 'On iPhone/iPad (Safari)'" class="text-lg space-y-4">
                <ol class="list-decimal pl-5 space-y-2">
                  <li>Open <span class="font-semibold">Safari</span> and visit the app URL.</li>
                  <li>Tap the <span class="font-semibold">Share</span> icon.</li>
                  <li>Select <span class="font-semibold">Add to Home Screen</span>.</li>
                  <li>Open it from your home screen like a normal app.</li>
                  <li class="opacity-80">Your data stays on your iPhone unless you export/import.</li>
                </ol>
              </div>

              <!-- Desktop -->
              <div v-else class="text-lg space-y-4">
                <ol class="list-decimal pl-5 space-y-2">
                  <li>Open <span class="font-semibold">Chrome</span> or <span class="font-semibold">Edge</span> and
                    visit the app URL.</li>
                  <li>Click the <span class="font-semibold">Install</span> icon in the address bar (if available).</li>
                  <li>Launch it like a desktop app from your applications list.</li>
                  <li class="opacity-80">Your data stays on this laptop unless you export/import.</li>
                </ol>
              </div>
            </template>
          </UAccordion>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <footer class="flex flex-col md:flex-row items-center justify-between px-4 py-4 gap-2 dark:text-gray-400">
      <div class="flex items-center space-x-1">
        <span>Personal fork</span>
        <UIcon class="text-rose-600" name="i-heroicons-heart-solid" />
        <span class="opacity-80">for private expense tracking</span>
      </div>

      <div class="flex items-center space-x-2 text-gray-800 dark:text-gray-400">
        <UButton to="https://github.com/rabinsang/splitTransaction" target="_blank" variant="ghost" color="gray"
          icon="fa6-brands-github" />
      </div>
    </footer>
  </div>
</template>

<script setup>
import Icon from "~/assets/logo.svg";

// Keep or remove this depending on your preference
definePageMeta({ colorMode: "dark" });

const features = [
  { text: "- Local-first and works offline (device storage)" },
  { text: "- Cross-platform: phone + laptop (browser or install as PWA)" },
  { text: "- Balances + simplified repayments (who owes who)" },
  { text: "- Clickable person details dashboard" },
  { text: "- Monthly + yearly summary per person" },
  { text: "- Full date & time in activity log" },
  { text: "- Import from Splitwise, export to CSV (backup)" },
  { text: "- Dark and light mode support" },
];
</script>
