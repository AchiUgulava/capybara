<template>
  <div class="o-header">
    <SfOverlay
      class="overlay"
      :visible=" isSearchPanelVisible"
      @click="$store.commit('ui/setSearchpanel', false)"
    />
    <div class="i-header">
      <div class="icon">
        <SfImage
          src="/assets/header-contact.svg"
          alt="logo"
          class="sf-header__icons"
        />
      </div>
      <div class="icon">
        <SfImage
          src="/assets/icons/header/icons.svg"
          alt="logo"
          class="sf-header__icons"
        />
      </div>
    </div>
    <SfHeader
      :active-icon="activeIcon"
      :class="{
        'sf-header--has-mobile-search': isSearchPanelVisible,
        'sf-header--is-sticky': isSearchPanelVisible
      }"
      :style="{'z-index': isHoveredMenu ? 2 : 1}"
    >
      <template #logo>
        <ALogo class="logo" />
      </template>
      <template #navigation>
        <SfHeaderNavigationItem
          v-for="category in categories"
          :key="category.id"
          @mouseover="isHoveredMenu = true"
          @mouseleave="isHoveredMenu = false"
          @click="isHoveredMenu = false"
        >
          <router-link
            :class="{active: isCategoryActive(category)}"
            :to="category.link"
          >
            {{ category.name }}
          </router-link>
          <!-- <MMenu
            :visible="isHoveredMenu && !isSearchPanelVisible"
            :categories-ids="category.children_data"
            :title="category.name"
            @close="isHoveredMenu = false"
          /> -->
        </SfHeaderNavigationItem>
      </template>
      <template #search>
        <div class="search-container">
          <OSearch :class="{'desktop-only': !isSearchPanelVisible}" />
          <SfIcon
            v-if="!isSearchPanelVisible"
            icon="search"
            size="xs"
            class="sf-header__icon"
            :style="{'cursor':'pointer'}"
            :class="{'desktop-only': !isSearchPanelVisible}"
            @click.native="$store.commit('ui/setSearchpanel', true)"
          />
          <SfButton
            v-else
            class="sf-button--text form__action-button form__action-button--secondary mobile-only"
            @click="$store.commit('ui/setSearchpanel', false)"
          >
            {{ $t("Cancel") }}
          </SfButton>
        </div>
      </template>
      <template #header-icons>
        <div class="sf-header__icons">
          <AMicrocartIcon class="sf-header__action" />
          <AAccountIcon class="sf-header__action" />
        </div>
      </template>
    </SfHeader>
    <MMenu
      v-show="isMobileMenu"
      class="mobile-menu"
      :categories-ids="categories"
      @close="$store.commit('ui/closeMenu')"
    />
  </div>
</template>

<script>
import { SfHeader, SfOverlay, SfButton, SfImage, SfIcon } from '@storefront-ui/vue';
import ALogo from 'theme/components/atoms/a-logo';
import AAccountIcon from 'theme/components/atoms/a-account-icon';
import AMicrocartIcon from 'theme/components/atoms/a-microcart-icon';
import OSearch from 'theme/components/organisms/o-search';
import { mapState, mapGetters } from 'vuex';
import MMenu from 'theme/components/molecules/m-menu';
import { formatCategoryLink } from '@vue-storefront/core/modules/url/helpers';
import { getTopLevelCategories } from 'theme/helpers';

export default {
  name: 'OHeader',
  components: {
    SfHeader,
    SfButton,
    SfImage,
    SfIcon,
    ALogo,
    AAccountIcon,
    AMicrocartIcon,
    OSearch,
    MMenu,
    SfOverlay
  },
  data () {
    return {
      isHoveredMenu: false,
      categories: [
        { id: '1', name: 'HOME', link: '/' },
        { id: '2', name: 'WOMEN', link: '/women.html' },
        { id: '3', name: 'MEN', link: '/men.html' },
        { id: '4', name: 'KIDS', link: '/kids.html' },
        { id: '5', name: 'JEWELLERY', link: '/jewellery.html' },
        { id: '6', name: 'ACCESORRIES', link: '/gear.html' }
      ]
    }
  },
  computed: {
    ...mapState({
      isSearchPanelVisible: state => state.ui.searchpanel
    }),
    ...mapState('ui', ['isMobileMenu']),
    ...mapGetters('category', ['getCategories', 'getCurrentCategory']),
    ...mapGetters('user', ['isLoggedIn']),

    ...mapGetters('url', ['getCurrentRoute']),
    activeIcon () {
      return this.isLoggedIn ? 'account' : '';
    }
    // categories () {
    //   return getTopLevelCategories(this.getCategories);
    // }
  },
  methods: {
    categoryLink (category) {
      // return formatCategoryLink(category);
    },
    isCategoryActive (category) {
      return category.link === this.getCurrentRoute.path
      // return this.getCurrentCategory.path ? this.getCurrentCategory.path.startsWith(category.path) : false;
    }
  },
  watch: {
    async isMobileMenu (status) {
      if (this.isMobileMenu) {
        // we can't add this style to body because sfui also add/remove overflow to body and there may be conflict
        document.documentElement.style.overflow = 'hidden'
      } else {
        document.documentElement.style.overflow = ''
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@import "~@storefront-ui/shared/styles/helpers/breakpoints";

.sf-header-navigation-item {
  &::after {
    bottom: 0;
    width: 0;
  }
  &:hover {
    .m-menu {
      opacity: 1;
      visibility: visible;
    }
    &::after {
      width: 100%;
    }
  }
}
.overlay {
  position: absolute;
  z-index: 1;
}
.o-header {
  --header-navigation-item-margin: 0 2rem 0 0;
  box-sizing: border-box;
  a {
    font-size: small;
    margin: 0;
    &.active {
      font-weight: bold;
      color: var(--c-primary);
    }
  }
  .search-container {
    display: flex;
    .o-search {
      flex-grow: 1;
    }
    @include for-mobile {
      width: 100%;
      .sf-button {
        margin: 0 0 0 var(--spacer-sm);
      }
    }
  }
  @include for-mobile {
    .mobile-menu {
      position: fixed;
      opacity: 1;
      visibility: visible;
      top: 0;
      z-index: 1;
      --mega-menu-aside-menu-height: calc(100vh - var(--bottom-navigation-height) - var(--bar-height));
    }
  }
}
.i-header{
  height: 42px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  background-color: black;
  color: antiquewhite;
  .icon{
    display: flex;
    flex-direction: row;
    font-size: x-small;
    padding: 0 3%;
  }
}

.sf-header {
  @include for-mobile {
    &__icons {
      display: none;
    }
  }
  @include for-desktop {
    &__icons {
      display: flex;
      .sf-header__icon {
        cursor: pointer;
      }
    }
  }
}
</style>
