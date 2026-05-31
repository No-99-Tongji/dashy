<template>
  <section>
    <SearchBar
      v-if="searchVisible"
      ref="SearchBar"
      @user-is-searchin="userIsTypingSomething"
    />
    <div class="options-outer" />

    <modal
      :name="modalNames.CONF_EDITOR"
      :resizable="true"
      width="80%"
      height="85%"
      classes="dashy-modal"
      @closed="onConfigClosed"
    >
      <ConfigContainer :config="combinedConfig" />
    </modal>
    <modal
      :name="modalNames.LANG_SWITCHER"
      :resizable="true"
      width="35%"
      height="60%"
      classes="dashy-modal"
    >
      <LanguageSwitcher />
    </modal>

    <AppInfoModal />
  </section>
</template>

<script>
import SearchBar from '@/components/Settings/SearchBar';
import AppInfoModal from '@/components/Configuration/AppInfoModal';
import ConfigContainer from '@/components/Configuration/ConfigContainer';
import LanguageSwitcher from '@/components/Settings/LanguageSwitcher';
import Keys from '@/utils/StoreMutations';
import { topLevelConfKeys, localStorageKeys, modalNames } from '@/utils/config/defaults';

export default {
  name: 'SettingsContainer',
  components: {
    SearchBar,
    AppInfoModal,
    ConfigContainer,
    LanguageSwitcher,
  },
  emits: ['user-is-searchin'],
  data: () => ({ modalNames }),
  computed: {
    searchVisible() {
      return this.$store.getters.visibleComponents.searchBar;
    },
    combinedConfig() {
      const app = this.$store.getters.appConfig;
      return {
        [topLevelConfKeys.APP_CONFIG]: {
          ...app,
          theme: localStorage[localStorageKeys.THEME] || app.theme,
        },
        [topLevelConfKeys.PAGE_INFO]: this.$store.getters.pageInfo,
        [topLevelConfKeys.SECTIONS]: this.$store.getters.sections,
      };
    },
  },
  methods: {
    userIsTypingSomething(q) { this.$emit('user-is-searchin', q); },
    clearFilterInput() {
      if (this.$refs.SearchBar) this.$refs.SearchBar.clearFilterInput();
    },
    onConfigClosed() { this.$store.commit(Keys.SET_MODAL_OPEN, false); },
  },
};
</script>

<style scoped lang="scss">
section {
  position: relative;
  display: flex;
  align-items: stretch;
  justify-content: flex-end;
  background: linear-gradient(0deg, var(--background) 0%, var(--background-darker) 100%);
  box-shadow: var(--settings-container-shadow);
}

.options-outer {
  display: none;
  align-items: center;
  justify-content: flex-end;
  flex: 1;
  padding: 0.25rem 0.5rem;
  background: var(--settings-background);
  border-radius: var(--curve-factor-navbar) 0 0;
}
</style>
