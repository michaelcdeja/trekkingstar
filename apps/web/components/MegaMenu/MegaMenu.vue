<template>
  <header ref="referenceRef" class="relative w-full h-full z-40 md:sticky md:shadow-md">
   <div
  class="flex justify-between items-center flex-wrap md:flex-nowrap px-4 md:px-10 py-2 md:py-5 w-full h-full border-0 bg-white border-neutral-200 md:h-20 md:z-10"
  data-testid="navbar-top"
>

      <div class="flex items-center">
        <UiButton
          v-if="viewport.isLessThan('lg')"
          variant="tertiary"
          square
          :aria-label="t('closeMenu')"
          class="mr-5 bg-transparent hover:bg-primary-800 hover:text-white active:bg-primary-700 active:text-white"
          @click="openMenu([])"
        >
          <SfIconMenu class="text-white" />
        </UiButton>

        <NuxtLink
          :to="localePath(paths.home)"
          :aria-label="t('goToHomepage')"
          class="flex shrink-0 w-full h-8 lg:w-48 lg:h-8 items-center mr-auto text-white md:mr-10 focus-visible:outline focus-visible:outline-offset focus-visible:rounded-sm"
        >
          <UiVsfLogo />
        </NuxtLink>
      </div>

      <slot />
    </div>

    <!-- Hauptkategorien -->
    <nav v-if="viewport.isGreaterOrEquals('lg')" ref="floatingRef">
      <ul
        class="flex px-6 py-2 bg-white border-b border-b-neutral-200 border-b-solid w-[80%] mx-auto"
        @blur="(event) => { if (!(event.currentTarget as Element).contains(event.relatedTarget as Element)) { close(); } }"
      >
        <!-- Hauptkategorien durchlaufen -->
        <li v-for="(category, index) in mainCategories" :key="index">
          <NuxtLink :to="category.link">
            <UiButton
              ref="triggerReference"
              variant="tertiary"
              data-testid="category-button"
              class="group mr-2 !text-neutral-900 hover:!text-orange-500 active:!text-orange-600"
              @mouseenter="category.childCount > 0 ? openMenu([category.id]) : openMenu([])"
              @click="category.childCount > 0 ? openMenu([category.id]) : openMenu([])"
            >
              <span>{{ category.title }}</span>
              <SfIconChevronRight
                v-if="category.childCount > 0"
                class="rotate-90 text-neutral-500 group-hover:text-neutral-700 group-active:text-neutral-900"
              />
            </UiButton>
          </NuxtLink>

          <!-- Mega-Menü für Unterkategorien -->
          <div
            v-if="isOpen && activeNode.length === 1 && activeNode[0] === category.id && category.childCount > 0"
            :key="category.id"
            ref="megaMenuReference"
            :style="style"
            class="hidden md:grid gap-x-6 grid-cols-4 bg-white shadow-lg p-6 left-0 right-0 outline-none z-40"
            tabindex="0"
            @mouseleave="close()"
            @keydown.esc="focusTrigger(index)"
          >
            <template v-for="node in category.children" :key="node.id">
              <div>
                <SfListItem
                  :tag="NuxtLink"
                  size="sm"
                  :href="localePath(generateCategoryLink(node))"
                  class="typography-text-base font-medium text-neutral-900 whitespace-nowrap px-4 py-1.5 border-b border-b-neutral-200 border-b-solid"
                >
                  {{ node.title }}
                </SfListItem>
                <ul class="mt-2">
                  <li v-for="child in node.children" :key="child.id">
                    <SfListItem
                      :tag="NuxtLink"
                      size="sm"
                      :href="localePath(generateCategoryLink(child))"
                      class="typography-text-sm py-1.5"
                    >
                      {{ child.title }}
                    </SfListItem>
                  </li>
                </ul>
              </div>
            </template>
          </div>
        </li>
      </ul>
    </nav>

    <!-- Drawer-Menü für mobile Geräte -->
    <template v-else>
      <div v-if="isOpen" class="fixed z-[50] inset-0 bg-neutral-500 bg-opacity-50" />
      <SfDrawer
        ref="drawerReference"
        v-model="isOpen"
        placement="left"
        class="right-12 max-w-96 bg-white overflow-y-auto z-[1000]"
      >
        <nav>
          <div class="flex items-center justify-between p-4 border-b border-b-neutral-200 border-b-solid">
            <p class="typography-text-base font-medium">Browse products</p>
            <UiButton variant="tertiary" square :aria-label="t('closeMenu')" class="ml-2" @click="close()">
              <SfIconClose class="text-neutral-500" />
            </UiButton>
          </div>
          <ul class="mt-2 mb-6">
            <li v-for="(category, index) in mainCategories" :key="index">
              <SfListItem
                size="lg"
                :tag="NuxtLink"
                :href="category.link"
                class="typography-text-base font-medium"
                @click="close()"
              >
                {{ category.title }}
              </SfListItem>
            </li>
          </ul>
        </nav>
      </SfDrawer>
    </template>
  </header>
</template>

<script setup lang="ts">
import { type CategoryTreeItem } from '@plentymarkets/shop-api';
import {
  SfIconClose,
  SfDrawer,
  SfListItem,
  SfIconChevronRight,
  SfCounter,
  SfIconArrowBack,
  SfIconMenu,
  useTrapFocus,
  useDropdown,
} from '@storefront-ui/vue';
import { unrefElement } from '@vueuse/core';
import { paths } from '~/utils/paths';

const { t } = useI18n();
const viewport = useViewport();
const localePath = useLocalePath();
const NuxtLink = resolveComponent('NuxtLink');
const props = defineProps();

// Hauptkategorien, die im Header angezeigt werden
const mainCategories = [
  { title: 'Damen', link: '/damen', id: 1, childCount: 0, children: [] },
  { title: 'Herren', link: '/herren', id: 2, childCount: 0, children: [] },
  { title: 'Kinder', link: '/kinder', id: 3, childCount: 0, children: [] },
  { title: 'Ausrüstung', link: '/ausruestung', id: 4, childCount: 0, children: [] },
  { title: 'Sale %', link: '/sale', id: 5, childCount: 0, children: [] },
];

// Mega-Menü Logik
const { close, open, isOpen, activeNode, setCategory } = useMegaMenu();
const { referenceRef, floatingRef, style } = useDropdown({
  isOpen,
  onClose: close,
  placement: 'bottom-start',
  middleware: [],
});

const drawerReference = ref();
const megaMenuReference = ref();
const triggerReference = ref();

// Öffnen des Mega-Menüs für Unterkategorien
const openMenu = (menuType: number[]) => {
  activeNode.value = menuType;
  open();
};

// Zurück im mobilen Menü
const goBack = () => {
  activeNode.value = activeNode.value.slice(0, -1);
};

// Weiter im mobilen Menü
const goNext = (key: number) => {
  activeNode.value = [...activeNode.value, key];
};

// Fokussieren der Trigger bei Navigation
const focusTrigger = (index: number) => {
  unrefElement(triggerReference.value[index]).focus();
};

// Setzt die Kategorie für das Menü
setCategory(mainCategories);
</script>
