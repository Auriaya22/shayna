<template>
  <div class="home">
    <HeaderShayna />

    <HeroShayna />

    <WomanShayna />

    <InstaShayna />

    <PartnerShayna />

    <FooterShayna />
  </div>
</template>

<script>
// @ is an alias to /src
import FooterShayna from '@/components/FooterShayna.vue';
import HeaderShayna from '@/components/HeaderShayna.vue';
import HeroShayna from '@/components/HeroShayna.vue';
import InstaShayna from '@/components/InstaShayna.vue';
import PartnerShayna from '@/components/PartnerShayna.vue';
import WomanShayna from '@/components/WomanShayna.vue';

export default {
  name: 'HomeView',
  components: {
    FooterShayna,
    HeaderShayna,
    HeroShayna,
    InstaShayna,
    PartnerShayna,
    WomanShayna,
  }
}
</script>
