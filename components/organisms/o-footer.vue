<template>
  <footer class="o-footer">
    <div class="footer-top">
      <div class="subscribe">
        <h2>
          Get our Special Discount
        </h2>
        <p>register your email for news and special offers</p>
        <div class="actions-box">
          <input type="text" placeholder="E-mail Address...">
          <button
            class="color-primary sf-button"
            :aria-disabled="false"
            :link="null"
            type="button"
            aria-label="button"
          >
            Get Coupon Now
          </button>
        </div>
      </div>
      <SfImage
        src="/assets/Clients.png"
        alt="logo"
        class="clients"
      />
    </div>
    <div class="footer-mid">
      <div class="paragraph">
        <p>we are confident we've provided all the best for you. Stay connected with us</p>
      </div>
      <div class="i-social">
        <SfImage
          src="/assets/social.png"
          alt="logo"
          class="sf-header__icons"
          :height="15"
        />
      </div>
    </div>
    <SfFooter :column="4" :multiple="true">
      <SfFooterColumn
        v-for="linkGroup in links"
        :key="linkGroup.name"
        :title="$t(linkGroup.name)"
      >
        <SfList>
          <SfListItem v-for="link in linkGroup.children" :key="link.name">
            <router-link v-if="link.link" :to="localizedRoute(link.link)" exact>
              <SfMenuItem class="sf-footer__menu-item" :label="$t(link.name)" />
            </router-link>
            <SfMenuItem
              v-else-if="link.clickHandler"
              class="sf-footer__menu-item"
              :label="$t(link.name)"
              @click="link.clickHandler"
            />
          </SfListItem>
        </SfList>
      </SfFooterColumn>
      <SfFooterColumn :title="$t('CONTACT INFO')">
        <!-- <SfList>
          <SfListItem>
            <router-link to="/legal" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Legal notice')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/privacy" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Privacy policy')"
              />
            </router-link>
          </SfListItem>
          <SfListItem v-if="multistoreEnabled">
            <SfMenuItem
              @click.native="showLanguageSwitcher"
              class="sf-footer__menu-item"
              :label="currentLanguage"
            />
          </SfListItem>
          <SfListItem class="sf-footer__menu-item">
            {{ getVersionInfo }}
          </SfListItem>
        </SfList> -->
        <SfImage
          src="/assets/contact-info.png"
          alt="logo"
          class="clients"
        />
      </SfFooterColumn>
    </SfFooter>
    <div class="i-footer">
      <div class="i-contact">
        <p>some copyright stuff - my copyright 2022</p>
      </div>
      <div class="i-visa">
        <SfImage
          src="/assets/card.png"
          alt="logo"
          class="sf-header__icons"
          :height="15"
        />
      </div>
    </div>
    <ABackToTop bottom="20px" right="20px" visibleoffset="200" class="desktop-only" />
  </footer>
</template>

<script>
import { mapActions, mapGetters } from 'vuex';
import ABackToTop from 'theme/components/atoms/a-back-to-top';
import { SfFooter, SfList, SfMenuItem, SfImage } from '@storefront-ui/vue';
import { ModalList } from 'theme/store/ui/modals'
import { getPathForStaticPage } from 'theme/helpers';
import config from 'config';
import { currentStoreView } from '@vue-storefront/core/lib/multistore';
import get from 'lodash-es/get';

export default {
  name: 'OFooter',
  components: {
    ABackToTop,
    SfFooter,
    SfList,
    SfMenuItem,
    SfImage
  },
  data () {
    return {
      social: ['facebook', 'pinterest', 'twitter', 'youtube']
    };
  },
  computed: {
    ...mapGetters('user', ['isLoggedIn']),
    multistoreEnabled () {
      return get(config, 'storeViews.multistore', false);
    },
    getVersionInfo () {
      return `v${process.env.__APPVERSION__} ${process.env.__BUILDTIME__}`;
    },
    currentLanguage () {
      const { i18n = config.i18n } = currentStoreView();
      return `${i18n.defaultCountry} / ${i18n.defaultLanguage} / ${i18n.currencyCode}`;
    },
    links () {
      return {
        // orders: {
        //   name: 'Orders',
        //   children: [
        //     {
        //       name: 'My account',
        //       ...this.isLoggedIn
        //         ? { link: '/my-account' }
        //         : { clickHandler: () => this.openModal({ name: ModalList.Auth, payload: 'login' }) }
        //     },
        //     { name: 'Delivery', link: '/delivery' },
        //     { name: 'Return policy', link: '/returns' }
        //   ]
        // },
        information: {
          name: 'Information',
          children: [
            {
              name: 'Discount', link: '/discount' },
            { name: 'Delivery Information', link: '/delivery' },
            { name: 'Sitemap', link: '/store-locator' },
            { name: 'Privacy Policy', link: '/privacy' },
            {
              name: 'My account',
              ...this.isLoggedIn
                ? { link: '/my-account' }
                : { clickHandler: () => this.openModal({ name: ModalList.Auth, payload: 'login' }) }
            }
          ]
        },
        myAccount: {
          name: 'My Account',
          children: [
            { name: 'Sign In',
              ...this.isLoggedIn
                ? { link: '/my-account' }
                : { clickHandler: () => this.openModal({ name: ModalList.Auth, payload: 'login' }) }
            },
            { name: 'View Cart', link: '/cart' },
            { name: 'My Wishlist', link: '/wish-list' },
            { name: 'Check Out', link: '/check-out' },
            { name: 'Track My Order', link: '/order' }
          ]
        },
        help: {
          name: 'Help',
          children: [
            { name: 'F.A.Q', link: '/faq' },
            { name: 'Shipping', link: '/shipping' },
            { name: 'Contact Us', link: '/about-us' },
            { name: 'Privacy Policy', link: '/Privacy' }
          ]
        }
        // about: {
        //   name: 'About us',
        //   children: [
        //     {
        //       name: 'About us',
        //       link: getPathForStaticPage('/about-us')
        //     },
        //     {
        //       name: 'Customer service',
        //       link: getPathForStaticPage('/customer-service')
        //     },
        //     { name: 'Store locator', link: '/store-locator' }
        //   ]
        // }
      };
    }
  },
  methods: {
    ...mapActions('ui', {
      openModal: 'openModal'
    }),
    showLanguageSwitcher () {
      this.openModal({ name: ModalList.LanguageSwitcher })
    }
  }
};
</script>

<style lang="scss" scoped>
@import "~@storefront-ui/shared/styles/helpers/breakpoints";
.footer-top{
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  flex-wrap: wrap-reverse;
  @include for-desktop {
    flex-wrap: nowrap;
  }
  width: 100%;
}
.subscribe{
  width: 100%;
  @include for-desktop {
    max-width: 50%;
  }
  background-color: rgb(30, 30, 61);
  color: white;
  padding: 10px;
  text-align: center;
  .actions-box{
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    justify-content: center;
    gap: 10px;
  }
  input{
    color: black;
    background-color: white;
    width: 50%;
    border: none;
    padding: 16px;
    margin: 10px 0px;
  }
}
.clients{
  width: 100%;
  @include for-desktop {
    max-width: 50%;
  }
  }
  .footer-mid{
  height: 50px;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
    border-bottom: 1px gray solid;
  .paragraph{
    display: flex;
    flex-direction: row;
    padding:5px 0px 5px;
    font-size: small;
    font-weight: 400;
    color: balck;
  }
  .i-social{
    display: flex;
    flex-direction: column;
    justify-content: center;

  }
}
.o-footer {
  .sf-footer {
    --footer-width: auto;
    @include for-desktop {
      max-width: 1272px;
      margin: auto;
      padding-left: 30px;
    }
  }
}
.social-column {
  flex-basis: auto;
}
.social-icon {
  padding: var(--spacer-sm) var(--spacer-xl);
  @include for-desktop {
    padding: var(--spacer-xs) 0;
  }
  &__img {
    height: 1.75rem;
    &:not(:last-child) {
      margin-right: var(--spacer-base);
    }
  }
}

.i-footer{
  height: 55px;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  background-color: black;
  color: antiquewhite;

  .i-contact{
    display: flex;
    flex-direction: row;
    padding:5px 0px 5px;
    font-size: small;
    font-weight: 600;
    color: gray;
  }
  .i-visa{
    display: flex;
    flex-direction: column;
    justify-content: center;

  }
}
</style>
