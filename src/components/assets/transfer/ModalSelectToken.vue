<template>
  <modal-wrapper
    :is-modal-open="isModalSelectToken"
    :title="$t('assets.transferPage.selectToken')"
    :is-closing="isClosingModal"
    :close-modal="closeModal"
    class-name="transfer-modal"
  >
    <div class="container--select-item-mobile">
      <div class="container--items">
        <div
          v-for="t in tokens"
          :key="t.id"
          class="row--item"
          :class="token.metadata.symbol === t.metadata.symbol && 'row--item-selected'"
          @click="setToken(t)"
        >
          <div class="column--item-name">
            <jazzicon
              v-if="t.tokenImage === 'custom-token'"
              :address="t.mappedERC20Addr"
              :diameter="24"
              class="item-logo"
            />
            <img
              v-else
              :src="t.tokenImage"
              :alt="t.metadata.symbol"
              :class="[t.metadata.symbol === nativeTokenSymbol ? 'native-token-logo' : 'item-logo']"
            />
            <span class="text--item-name">{{ t.metadata.symbol }}</span>
          </div>
          <div class="column--item-name">
            <span class="text--token-amount">
              <token-balance :balance="String(t.userBalance)" :symbol="t.metadata.symbol" />
            </span>
          </div>
        </div>
      </div>
    </div>
  </modal-wrapper>
</template>
<script lang="ts">
import { fadeDuration } from '@astar-network/astar-ui';
import ModalWrapper from 'src/components/common/ModalWrapper.vue';
import { useNetworkInfo } from 'src/hooks';
import { wait } from 'src/v2/common';
import { Asset } from 'src/v2/models';
import { defineComponent, PropType, ref } from 'vue';
import { truncate } from 'src/hooks/helper/common';
import TokenBalance from 'src/components/common/TokenBalance.vue';
import Jazzicon from 'vue3-jazzicon/src/components';

export default defineComponent({
  components: {
    ModalWrapper,
    TokenBalance,
    [Jazzicon.name]: Jazzicon,
  },
  props: {
    isModalSelectToken: {
      type: Boolean,
      required: true,
    },
    handleModalSelectToken: {
      type: Function,
      required: true,
    },
    setToken: {
      type: Function,
      required: true,
    },
    tokens: {
      type: Object as PropType<Asset[]>,
      required: true,
    },
    token: {
      type: Object as PropType<Asset>,
      required: true,
    },
  },
  setup(props) {
    const isClosingModal = ref<boolean>(false);
    const closeModal = async (): Promise<void> => {
      isClosingModal.value = true;
      await wait(fadeDuration);
      props.handleModalSelectToken({ isOpen: false });
      isClosingModal.value = false;
    };

    const { nativeTokenSymbol } = useNetworkInfo();

    return { nativeTokenSymbol, closeModal, isClosingModal, truncate };
  },
});
</script>

<style lang="scss" scoped>
@use 'src/components/assets/transfer/styles/select-chain.scss';
</style>
