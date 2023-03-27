<script lang="ts">
  import { ethers } from 'ethers';
  import { ArrowTopRightOnSquare } from 'svelte-heros-v2';
  import Modal from '../modals/Modal.svelte';
  import type { BridgeTransaction } from '../../domain/transactions';
  import { addressSubsection } from '../../utils/addressSubsection';
  import { chains } from '../../chain/chains';

  export let transaction: BridgeTransaction;
  export let onClose: () => void;

  $: hasTransaction = Boolean(transaction);
</script>

<Modal title="Transaction Details" {onClose} isOpen={hasTransaction}>
  <!-- 
    Content of this modal depends on the transaction.
    If not transaction is passed in, don't render the table.
  -->
  {#if hasTransaction}
    {@const { fromChainId, hash, message } = transaction}
    {@const {
      sender,
      owner,
      refundAddress,
      callValue,
      processingFee,
      gasLimit,
      memo,
    } = message}

    <table
      class="
        table 
        table-normal 
        w-full 
        md:w-2/3 
        m-auto 
        table-fixed 
        border-spacing-0 
        text-sm md:text-base
      ">
      <tr>
        <td>Tx Hash</td>
        <td class="text-right">
          <a
            class="link flex items-center justify-end"
            target="_blank"
            rel="noreferrer"
            href={`${chains[fromChainId].explorerUrl}/tx/${hash}`}>
            <span class="mr-1">{addressSubsection(hash)}</span>
            <ArrowTopRightOnSquare />
          </a>
        </td>
      </tr>

      {#if message}
        <tr>
          <td>Sender</td>
          <td class="text-right">
            {addressSubsection(sender)}
          </td>
        </tr>

        <tr>
          <td>Owner</td>
          <td class="text-right">
            {addressSubsection(owner)}
          </td>
        </tr>

        <tr>
          <td>Refund Address</td>
          <td class="text-right">
            {addressSubsection(refundAddress)}
          </td>
        </tr>

        {#if callValue}
          <tr>
            <td>Call value</td>
            <td class="text-right">
              {ethers.utils.formatEther(callValue.toString())}
              ETH
            </td>
          </tr>
        {/if}

        {#if processingFee}
          <tr>
            <td>Processing Fee</td>
            <td class="text-right">
              {ethers.utils.formatEther(processingFee)} ETH
            </td>
          </tr>
        {/if}

        <tr>
          <td>Gas Limit</td>
          <td class="text-right">
            {gasLimit}
          </td>
        </tr>
        <tr>
          <td>Memo</td>
          <td class="text-right">
            <div class="overflow-auto">
              {memo}
            </div>
          </td>
        </tr>
      {/if}
    </table>
  {/if}
</Modal>
