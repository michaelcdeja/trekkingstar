<template>
  <div class="relative min-h-[600px]">
    <div class="absolute w-full h-full z-[-1]">
      <img
        :src="background.image"
        :width="getSizeForViewport(background.sizes).width"
        :height="getSizeForViewport(background.sizes).height"
        :alt="background.alt"
        class="absolute top-0 left-0 w-full h-full object-cover"
      />
    </div>
    <div class="md:flex md:flex-row-reverse md:justify-center max-w-[1536px] mx-auto md:min-h-[600px]">
      <div class="flex flex-col md:basis-2/4 md:items-stretch md:overflow-hidden">
        <img
          :src="DidriksonsBanner.image"
          :width="getSizeForViewport(DidriksonsBanner.sizes).width"
          :height="getSizeForViewport(DidriksonsBanner.sizes).height"
          :alt="DidriksonsBanner.alt"
          class="h-full object-cover object-left"
        />
      </div>
      <div class="p-4 md:p-10 md:max-w-[768px] md:flex md:flex-col md:justify-center md:items-start md:basis-2/4">
        <p class="typography-text-xs md:typography-text-sm font-bold tracking-widest text-neutral-500 uppercase">
          {{ t('homepage.banner.moto1') }}
        </p>
        <h1 class="typography-display-2 md:typography-display-1 md:leading-[67.5px] font-bold mt-2 mb-4">
          {{ t('homepage.banner.moto2') }}
        </h1>
        <p class="typography-text-base md:typography-text-lg">
          {{ t('homepage.banner.moto3') }}
        </p>
        <div class="flex flex-col md:flex-row gap-4 mt-6">
          <UiButton size="lg"> {{ t('homepage.banner.orderNow') }}</UiButton>
          <UiButton size="lg" variant="secondary" class="bg-white"> {{ t('homepage.banner.showMore') }}</UiButton>
        </div>
      </div>
    </div>
  </div>
  <div class="max-w-screen-3xl mx-auto md:px-6 lg:px-10">
    <!-- Kategorien (Damen, Herren, Kinder, Ausrüstung) in einer Reihe, quadratisch -->
    <div class="flex justify-center gap-4 lg:gap-6 my-10">
      <div
        v-for="{ title, ariaLabel, image } in categories.slice(0, 4)"
        :key="title"
        role="img"
        :aria-label="ariaLabel"
        :aria-labelledby="`image-${title}`"
        class="relative flex-col w-[250px] h-[250px] justify-center group"
      >
        <img
          :src="image"
          :alt="ariaLabel"
          format="avif"
          class="bg-neutral-100 group-hover:shadow-xl group-active:shadow-none object-cover w-full h-full"
          width="250"
          height="250"
          loading="lazy"
        />
        <div :id="`image-${title}`" class="flex justify-center">
          <div
            class="mt-4 font-semibold no-underline text-normal-900 typography-text-base group-hover:text-primary-800 group-active:text-primary-800"
          >
            {{ title }}
          </div>
        </div>
      </div>
    </div>

    <!-- Sale% Kategorie -->
    <div class="flex justify-center my-10">
      <div
        v-for="{ title, ariaLabel, image } in categories.slice(4, 5)"
        :key="title"
        role="img"
        :aria-label="ariaLabel"
        :aria-labelledby="`image-${title}`"
        class="relative flex-col w-full max-w-[1080px] h-[250px] justify-center group"
      >
        <img
          :src="image"
          :alt="ariaLabel"
          format="avif"
          class="bg-neutral-100 group-hover:shadow-xl group-active:shadow-none object-cover w-full h-full"
          loading="lazy"
        />
        <div :id="`image-${title}`" class="flex justify-center">
          <div
            class="mt-4 font-semibold no-underline text-normal-900 typography-text-base group-hover:text-primary-800 group-active:text-primary-800"
          >
            {{ title }}
          </div>
        </div>
      </div>
    </div>

    <NuxtLazyHydrate when-visible>
      <NewsletterSubscribe v-if="showNewsletter" />
    </NuxtLazyHydrate>

    <NuxtLazyHydrate when-visible>
      <section class="mx-4 mt-28 mb-20 overflow-hidden">
        <p data-testid="recommended-products" class="my-4 typography-text-lg">
          {{ t('moreItemsOfThisCategory') }}
        </p>
        <ProductRecommendedProducts cache-key="homepage" :category-id="recommendedProductsCategoryId" />
      </section>
    </NuxtLazyHydrate>
  </div>
</template>

<script lang="ts" setup>
const viewport = useViewport();
const { t } = useI18n();
const { data: categoryTree } = useCategoryTree();
const recommendedProductsCategoryId = ref('');
definePageMeta({ pageType: 'static' });

type Size = {
  width: string;
  height: string;
};
type Sizes = {
  lg: Size;
  md: Size;
  sm: Size;
};
type SizeKey = keyof Sizes;

const getSizeForViewport = (sizes: Sizes) => {
  const breakpoint = viewport.breakpoint.value as SizeKey;
  return sizes[breakpoint];
};

watch(
  () => categoryTree.value,
  async () => {
    const firstCategoryId = categoryTree.value?.[0]?.id;
    if (firstCategoryId) recommendedProductsCategoryId.value = firstCategoryId.toString();
  },
  { immediate: true }
);

const { showNewsletter } = useNewsletter();
const DidriksonsBanner = {
  image: ``,
  alt: '',
  sizes: {
    lg: { width: '800', height: '600' },
    md: { width: '800', height: '600' },
    sm: { width: '640', height: '480' },
  },
};

const background = {
  image: `https://cdn02.plentymarkets.com/gfckbh0ooc5t/frontend/img/banner/Didriksons_Herstellerbanner_Winter2023.jpg`,
  alt: t('homepage.background'),
  sizes: {
    lg: { width: '4000', height: '600' },
    md: { width: '1024', height: '600' },
    sm: { width: '640', height: '752' },
  },
};

const categories = [
  {
    title: 'Damen',
    ariaLabel: 'Damen Homepage Kategorie',
    image: '/images/homepage-women-category.avif',
  },
  {
    title: 'Herren',
    ariaLabel: 'Herren Homepage Kategorie',
    image: '/images/homepage-men-category.avif',
  },
  {
    title: 'Kinder',
    ariaLabel: 'Kinder Homepage Kategorie',
    image: '/images/homepage-kid-category.avif',
  },
  {
    title: 'Ausrüstung',
    ariaLabel: 'Ausrüstung Homepage Kategorie',
    image: '/images/homepage-equipment-category.avif',
  },
  {
    title: 'Sale %',
    ariaLabel: 'Sale Prozent Kategorie',
    image: '/images/SALE.png',
  },
];

useHead({
  link: [
    { rel: 'preload', href: background.image, as: 'image' },
    { rel: 'preload', href: DidriksonsBanner.image, as: 'image' },
  ],
});
</script>
