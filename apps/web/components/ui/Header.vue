<template>
  <!-- Zusätzliche Leiste über dem Header -->
  <div class="bg-gray-900 text-gray-100 py-2">
    <div class="container mx-auto flex justify-center items-center flex-wrap text-xs space-x-8">
      <!-- Icons und Text zentriert -->
      <!-- Versandkostenfrei -->
      <div class="flex items-center">
        <a href="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/box.svg" target="_blank" class="mr-1">
          <img src="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/box.svg" alt="Versandkostenfrei" class="w-4 h-4">
        </a>
        <div class="flex flex-col leading-tight">
          <span>Versandkostenfrei</span>
          <span>ab 39,95 € in DE & AT</span>
        </div>
      </div>

      <!-- Kostenfreie Rücksendung -->
      <div class="flex items-center">
        <a href="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/arrows.svg" target="_blank" class="mr-1">
          <img src="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/arrows.svg" alt="Rücksendung Icon" class="w-4 h-4">
        </a>
        <div class="flex flex-col leading-tight">
          <span>Kostenfreie</span>
          <span>Rücksendung</span>
        </div>
      </div>

      <!-- 30 Tage Rückgaberecht -->
      <div class="flex items-center">
        <a href="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/reload.svg" target="_blank" class="mr-1">
          <img src="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/reload.svg" alt="Rückgaberecht Icon" class="w-4 h-4">
        </a>
        <div class="flex flex-col leading-tight">
          <span>30 Tage</span>
          <span>Rückgaberecht</span>
        </div>
      </div>

      <!-- Rechnungskauf/Ratenkauf -->
      <div class="flex items-center">
        <a href="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/page.svg" target="_blank" class="mr-1">
          <img src="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/page.svg" alt="Rechnungskauf/Ratenkauf Icon" class="w-4 h-4">
        </a>
        <div class="flex flex-col leading-tight">
          <span>Rechnungskauf</span>
          <span>/Ratenkauf</span>
        </div>
      </div>

      <!-- Positive Bewertungen -->
      <div class="flex items-center">
        <a href="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/tag.svg" target="_blank" class="mr-1">
          <img src="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/tag.svg" alt="Positive Bewertungen Icon" class="w-4 h-4">
        </a>
        <div class="flex flex-col leading-tight">
          <span>Über 100.000</span>
          <span>positive Bewertungen</span>
        </div>
      </div>

      <!-- Telefonkontakt -->
      <div class="flex items-center">
        <a href="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/phone.svg" target="_blank" class="mr-1">
          <img src="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/header/phone.svg" alt="Telefon Icon" class="w-4 h-4">
        </a>
        <div class="flex flex-col leading-tight">
          <span>+49 (0)209 - 728 47</span>
          <span>(Mo - Fr: 8:00 - 16:30 Uhr)</span>
        </div>
      </div>
    </div>
  </div>

  <!-- MegaMenu-Komponente mit Logo zentriert -->
  <MegaMenu :categories="categoryTree">
    <template v-if="viewport.isGreaterOrEquals('md')">
      <div class="mx-auto w-[80%] bg-white flex items-center justify-center">
        <!-- Logo und Suche zentriert -->
        <div class="flex items-center justify-center w-full">
          <!-- Logo -->
          <div class="flex justify-center mr-8"> <!-- Vergrößerter Abstand mit mr-8 -->
            <NuxtImg src="https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/logo/trekking-star.png" alt="Trekking Star Logo" class="w-[210px] h-[80px] py-2" preload />
          </div>
          
          <!-- Verkleinerte Suchleiste -->
          <UiSearch class="flex-1 w-[50%] md:w-[30%]" />

          <!-- Sprachwahl und Wunschliste -->
          <div class="flex space-x-4 ml-4">
            <!-- Sprachwahl -->
            <UiButton
              v-if="!isLanguageSelectOpen"
              class="group relative text-black hover:text-orange-500 active:text-orange-700 rounded-md cursor-pointer"
              :aria-label="t('languageSelector')"
              variant="tertiary"
              square
              data-testid="open-languageselect-button"
              @click="toggleLanguageSelect"
            >
              <template #prefix>
                <SfIconLanguage class="relative" />
              </template>
            </UiButton>

            <!-- Wunschliste -->
            <UiButton
              class="group relative text-black hover:text-orange-500 active:text-orange-700 rounded-md"
              :tag="NuxtLink"
              :to="localePath(paths.wishlist)"
              :aria-label="t('numberInWishlist', { count: wishlistItemIds.length })"
              variant="tertiary"
              square
              data-testid="wishlist-page-navigation"
            >
              <template #prefix>
                <SfIconFavorite />
                <SfBadge
                  :content="wishlistItemIds.length"
                  class="outline outline-primary-500 bg-white !text-neutral-900 group-hover:outline-primary-800 group-active:outline-primary-700 flex justify-center text-xs !leading-3"
                  data-testid="wishlist-badge"
                />
              </template>
            </UiButton>

            <!-- Warenkorb -->
            <UiButton
              class="group relative text-black hover:text-orange-500 active:text-orange-700 rounded-md"
              :tag="NuxtLink"
              :to="localePath(paths.cart)"
              :aria-label="t('numberInCart', { count: cartItemsCount })"
              variant="tertiary"
              square
            >
              <template #prefix>
                <SfIconShoppingCart />
                <SfBadge
                  :content="cartItemsCount"
                  class="outline outline-primary-500 bg-white !text-neutral-900 group-hover:outline-primary-800 group-active:outline-primary-700 flex justify-center text-xs !leading-3"
                  data-testid="cart-badge"
                />
              </template>
            </UiButton>
          </div>
        </div>
      </div>
    </template>

    <!-- Mobile Ansicht -->
    <div v-if="viewport.isLessThan('lg')" class="bg-white w-full">
      <UiButton
        variant="tertiary"
        class="relative text-black hover:text-orange-500 active:text-orange-700 rounded-md md:hidden"
        square
        data-testid="open-languageselect-button"
        :aria-label="t('languageSelector')"
        @click="toggleLanguageSelect"
      >
        <SfIconLanguage />
      </UiButton>
      <UiButton
        variant="tertiary"
        class="relative text-black hover:text-orange-500 active:text-orange-700 rounded-md md:hidden"
        square
        @click="searchModalOpen"
        :aria-label="t('openSearchModalButtonLabel')"
      >
        <SfIconSearch />
      </UiButton>
    </div>
  </MegaMenu>
</template>

<script setup lang="ts">
import {
  SfBadge,
  SfDropdown,
  SfIconLanguage,
  SfIconPerson,
  SfIconFavorite,
  SfIconShoppingCart,
  useDisclosure,
} from '@storefront-ui/vue';
import { paths } from '~/utils/paths';
import { useCart, useWishlist, useCustomer } from '~/composables';

const { data: cart } = useCart();
const { wishlistItemIds } = useWishlist();
const cartItemsCount = ref(0);

const NuxtLink = resolveComponent('NuxtLink');
const { t } = useI18n();
const route = useRoute();
const localePath = useLocalePath();
const { isOpen: isAccountDropdownOpen, toggle: accountDropdownToggle } = useDisclosure();
const { isOpen: isAuthenticationOpen, open: openAuthentication, close: closeAuthentication } = useDisclosure();
const { open: searchModalOpen, isOpen: isSearchModalOpen, close: searchModalClose } = useDisclosure();
const { open } = useConfigurationDrawer();
const { toggle: toggleLanguageSelect, isOpen: isLanguageSelectOpen } = useLocalization();
const { data: categoryTree } = useCategoryTree();
const { data: user, isAuthorized, logout } = useCustomer();
const viewport = useViewport();
</script>
