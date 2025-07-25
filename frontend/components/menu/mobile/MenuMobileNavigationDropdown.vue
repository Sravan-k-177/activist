<!-- SPDX-License-Identifier: AGPL-3.0-or-later -->
<template>
  <div
    v-if="selectedMenuItem"
    id="submenu"
    class="fixed z-20 h-10 w-full bg-menu-selection"
  >
    <Listbox v-model="selectedMenuItem">
      <ListboxButton
        class="elem-shadow-sm focus-brand relative flex w-full items-center fill-layer-1 py-2 pl-5 text-left align-middle text-layer-1"
      >
        <Icon
          :name="selectedMenuItem.iconUrl"
          class="mr-4 h-5 w-5 align-middle"
          aria-hidden="true"
        />
        <span>{{ $t(selectedMenuItem.label) }}</span>
        <span
          class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-2"
        >
          <Icon
            :name="IconMap.CHEVRON_EXPAND"
            class="mr-2 h-5 w-5 align-middle"
            aria-hidden="true"
        /></span>
      </ListboxButton>
      <transition
        leave-active-class="transition duration-100 ease-in"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
      >
        <ListboxOptions class="focus-brand bg-menu-selection">
          <ListboxOption
            v-for="menuEntry in sidebarType === SidebarType.ORGANIZATION_PAGE
              ? menuEntryState.organizationEntry.value
              : menuEntryState.eventEntry.value"
            v-slot="{ selected }"
            :key="menuEntry.id"
            :value="menuEntry"
          >
            <NuxtLink @click="handleItemClick(menuEntry)">
              <li
                :id="
                  (sidebarType === SidebarType.ORGANIZATION_PAGE
                    ? 'org-'
                    : 'event-') + menuEntry.label.split('.').pop()
                "
                class="relative flex cursor-default select-none items-center py-2 pl-5 align-middle"
                :class="{
                  'bg-layer-2 fill-primary-text text-primary-text dark:bg-section-div':
                    selected,
                  'bg-highlight fill-layer-1 text-layer-1': !selected,
                }"
              >
                <Icon
                  :name="menuEntry.iconUrl"
                  class="mr-4 h-5 w-5 align-middle"
                  aria-hidden="true"
                />
                <span
                  class="block truncate"
                  :class="{ 'font-medium': selected, 'font-normal': !selected }"
                >
                  {{ $t(menuEntry.label) }}
                </span>
              </li>
            </NuxtLink>
          </ListboxOption>
        </ListboxOptions>
      </transition>
    </Listbox>
  </div>
</template>

<script setup lang="ts">
import {
  Listbox,
  ListboxButton,
  ListboxOption,
  ListboxOptions,
} from "@headlessui/vue";

import type MenuEntry from "~/types/menu/menu-entry";

import { IconMap } from "~/types/icon-map";
import { SidebarType } from "~/types/sidebar-type";
import {
  currentRoutePathIncludes,
  isCurrentRoutePathSubpageOf,
} from "~/utils/routeUtils";

const { currentRoute } = useRouter();

const routeName = computed(() => {
  if (currentRoute.value.name) {
    return currentRoute.value.name;
  }
  return "";
});

const isOrgPage = computed(() =>
  isCurrentRoutePathSubpageOf("organizations", routeName.value.toString())
);
const isEventPage = computed(() =>
  isCurrentRoutePathSubpageOf("events", routeName.value.toString())
);

const pathToSidebarTypeMap = [
  { path: "search", type: SidebarType.SEARCH },
  { path: "home", type: SidebarType.HOME },
  {
    path: "organizations",
    type: isOrgPage.value
      ? SidebarType.ORGANIZATION_PAGE
      : SidebarType.ORGANIZATIONS_PAGE,
  },
  {
    path: "events",
    type: isEventPage.value ? SidebarType.EVENT_PAGE : SidebarType.EVENTS_PAGE,
  },
];

watch([isOrgPage, isEventPage], () => {
  pathToSidebarTypeMap[2].type = isOrgPage.value
    ? SidebarType.ORGANIZATION_PAGE
    : SidebarType.ORGANIZATIONS_PAGE;
  pathToSidebarTypeMap[3].type = isEventPage.value
    ? SidebarType.EVENT_PAGE
    : SidebarType.EVENTS_PAGE;
});

const sidebarType = computed(() => {
  const matchingPath = pathToSidebarTypeMap.find((item) =>
    currentRoutePathIncludes(item.path, routeName.value.toString())
  );
  return matchingPath?.type || SidebarType.MISC;
});

const menuEntryState = useMenuEntriesState();
const selectedMenuItem = ref<MenuEntry | undefined>(undefined);

const handleItemClick = (menuEntry: MenuEntry) => {
  const router = useRouter();
  router.push(menuEntry.routeUrl);
};

watchEffect(() => {
  selectedMenuItem.value = useRouter().currentRoute.value.fullPath.includes(
    "/organizations/"
  )
    ? menuEntryState.organizationEntry.value.find((e) => e.selected)
    : menuEntryState.eventEntry.value.find((e) => e.selected);
});
</script>
