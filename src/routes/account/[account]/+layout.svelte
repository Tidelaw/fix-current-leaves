<script lang="ts">
    import { page } from "$app/stores";

    import Icon from "$lib/components/icon.svelte";

    import AccountHeader from "$lib/components/account-header.svelte";
    import { showModal } from "$lib/state/stores/modals";
    import { trpcWithQuery } from "$lib/trpc/client";
    import { PROGRAM_ID as ACCOUNT_COMPRESSION_ID } from "@solana/spl-account-compression";

    const client = trpcWithQuery($page);

    const account = $page.params.account;
    const accountInfo = client.accountInfo.createQuery(account);
</script>

<div class="relative mx-auto w-full max-w-2xl pb-32">
    <AccountHeader
        {account}
        link={$page.url.href}
    />

    <div>
        <div
            class="mx-3 mb-5 mt-3 flex items-center justify-between rounded-lg border"
        >
            <div class="tabs w-full pt-1 md:w-auto">
                <div />
                <a
                    href={`/account/${account}`}
                    class="tab-bordered tab"
                    class:tab-active={$page.url.pathname.endsWith(`${account}`)}
                    >Transactions</a
                >
                <a
                    href={`/account/${account}/tokens`}
                    class="tab-bordered tab"
                    class:tab-active={$page.url.pathname.endsWith("/tokens")}
                    >Tokens</a
                >
                {#if $accountInfo?.data?.value?.owner === ACCOUNT_COMPRESSION_ID.toBase58()}
                    <a
                        href={`/account/${account}/concurrent-merkle-tree`}
                        class="tab-bordered tab"
                        class:tab-active={$page.url.pathname.endsWith(
                            "concurrent-merkle-tree"
                        )}>Concurrent Merkle Tree</a
                    >
                {/if}
            </div>
            {#if !$page.url.pathname.endsWith("/tokens")}
                <button
                    class="btn-ghost btn-sm btn"
                    on:click={() => showModal("TRANSACTION_FILTER")}
                >
                    <Icon id="settings" />
                </button>
            {/if}
        </div>
    </div>

    <div class="content px-3">
        <slot />
    </div>
</div>
