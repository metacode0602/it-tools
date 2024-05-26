<script lang="ts" setup>
import { NIcon, useThemeVars } from 'naive-ui';

import { RouterLink } from 'vue-router';
import { Heart, Home2, Menu2 } from '@vicons/tabler';

import { storeToRefs } from 'pinia';
import HeroGradient from '../assets/hero-gradient.svg?component';
import MenuLayout from '../components/MenuLayout.vue';
import NavbarButtons from '../components/NavbarButtons.vue';
import { useStyleStore } from '@/stores/style.store';
import { config } from '@/config';
import type { ToolCategory } from '@/tools/tools.types';
import { useToolStore } from '@/tools/tools.store';
import { useTracker } from '@/modules/tracker/tracker.services';
import CollapsibleToolMenu from '@/components/CollapsibleToolMenu.vue';

const themeVars = useThemeVars();
const styleStore = useStyleStore();

const { tracker } = useTracker();
const { t } = useI18n();
const version = config.app.version;

const toolStore = useToolStore();
const { favoriteTools, toolsByCategory } = storeToRefs(toolStore);

const tools = computed<ToolCategory[]>(() => [
  ...(favoriteTools.value.length > 0 ? [{ name: t('tools.categories.favorite-tools'), components: favoriteTools.value }] : []),
  ...toolsByCategory.value,
]);
</script>

<template>
  <MenuLayout class="menu-layout" :class="{ isSmallScreen: styleStore.isSmallScreen }">
    <template #sider>
      <RouterLink to="/" class="hero-wrapper">
        <HeroGradient class="gradient" />
        <div class="text-wrapper">
          <div class="title">
            极客岛
          </div>
          <div class="divider" />
          <div class="subtitle">
            极客汇聚，码动未来
          </div>
        </div>
      </RouterLink>

      <div class="sider-content">
        <div v-if="styleStore.isSmallScreen" flex flex-col items-center>
          <locale-selector w="90%" />

          <div flex justify-center>
            <NavbarButtons />
          </div>
        </div>

        <CollapsibleToolMenu :tools-by-category="tools" />
      </div>
    </template>

    <template #content>
      <div flex items-center justify-center gap-2 class="header">
        <c-button
          circle
          variant="text"
          :aria-label="$t('home.toggleMenu')"
          @click="styleStore.isMenuCollapsed = !styleStore.isMenuCollapsed"
        >
          <NIcon size="25" :component="Menu2" />
        </c-button>

        <c-tooltip :tooltip="$t('home.home')" position="bottom">
          <c-button to="/" circle variant="text" :aria-label="$t('home.home')">
            <NIcon size="25" :component="Home2" />
          </c-button>
        </c-tooltip>

        <c-tooltip :tooltip="$t('home.uiLib')" position="bottom">
          <c-button to="/c-lib" circle variant="text" :aria-label="$t('home.uiLib')" href="https://www.julianshuke.top/ai.html" rel="noopener" target="_blank">
            <icon-mdi:brush-variant text-20px />
          </c-button>
        </c-tooltip>
        <c-tooltip :tooltip="$t('home.opensource')" position="bottom">
          <c-button
            circle
            variant="text"
            href="https://www.julianshuke.top/open-source.html"
            rel="noopener"
            target="_blank"
            :aria-label="$t('home.opensource')"
          >
            <IconMdi:kettleSteamOutline text-20px />
          </c-button>
        </c-tooltip>
        <command-palette />

        <!-- <locale-selector v-if="!styleStore.isSmallScreen" /> -->

        <div>
          <NavbarButtons v-if="!styleStore.isSmallScreen" />
        </div>

        <!-- <c-tooltip position="bottom" :tooltip="$t('home.contact')">
          <c-button
            round
            to="/about" :aria-label="$t('home.contact')"
            rel="noopener"
            class="support-button"
            :bordered="false"
            @click="() => tracker.trackEvent({ eventName: 'contact button clicked' })"
          >
            {{ $t('home.contact') }}
            <NIcon v-if="!styleStore.isSmallScreen" :component="Heart" ml-2 />
          </c-button>
        </c-tooltip> -->
      </div>
      <slot />
      <div class="footer">
        <div class="">
          <img src="/favicon-16x16.png" style="vertical-align: middle; padding-bottom:3px">
          <span class="text-neutral-500">
            极客岛 {{ version }}
          </span>
        </div>
        <div>
          <span class="text-neutral-500">
            © {{ new Date().getFullYear() }} 天津聚链科技有限公司版权所有
          </span>
          <c-link target="_blank" rel="noopener" href="https://beian.miit.gov.cn/">
            <span class="text-neutral-500">津ICP备2023007973号-1</span>
          </c-link>
        </div>
        <div>
          <img src="@/assets/gongan.png" style="vertical-align:middle;width:16px;padding-bottom: 4px;">
          <c-link target="_blank" href="http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=12011402001495">
            <span span class="text-neutral-500">
              津公网安备12011402001495号
            </span>
          </c-link>
        </div>
      </div>
    </template>
  </MenuLayout>
</template>

<style lang="less" scoped>
// ::v-deep(.n-layout-scroll-container) {
//     @percent: 4%;
//     @position: 25px;
//     @size: 50px;
//     @color: #eeeeee25;
//     background-image: radial-gradient(@color @percent, transparent @percent),
//         radial-gradient(@color @percent, transparent @percent);
//     background-position: 0 0, @position @position;
//     background-size: @size @size;
// }

.support-button {
  background: rgb(37, 99, 108);
  background: linear-gradient(48deg, rgba(37, 99, 108, 1) 0%, rgba(59, 149, 111, 1) 60%, rgba(20, 160, 88, 1) 100%);
  color: #fff !important;
  transition: padding ease 0.2s !important;

  &:hover {
    color: #fff;
    padding-left: 30px;
    padding-right: 30px;
  }
}

.sider-content {
  padding-top: 160px;
  padding-bottom: 200px;
}

.hero-wrapper {
  position: absolute;
  display: block;
  left: 0;
  width: 100%;
  z-index: 10;
  overflow: hidden;

  .gradient {
    margin-top: -65px;
  }

  .text-wrapper {
    position: absolute;
    left: 0;
    width: 100%;
    text-align: center;
    top: 16px;
    color: #fff;

    .title {
      font-size: 25px;
      font-weight: 600;
    }

    .divider {
      width: 50px;
      height: 2px;
      border-radius: 4px;
      background-color: v-bind('themeVars.primaryColor');
      margin: 0 auto 5px;
    }

    .subtitle {
      font-size: 16px;
    }
  }
}

.header {
  position: sticky;
  top: 0;
  opacity: 1;
  background-color: v-bind('themeVars.bodyColor');
  // background-color: #fff;
  padding: 15px;
  z-index: 100;
}

.footer {
  // position: sticky;
  bottom: 0;
  text-align: center;
  color: #838587;
  margin-top: 20px;
  padding: 10px 0;
  width: 100%;
}
</style>
