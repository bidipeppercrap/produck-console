<script>
    import { fetchServer } from "$lib/fetch.js";
    import { displayMargin } from "$lib/marginCalculator";
    import { toStringDelimit } from "$lib/numbering";

    import Pagination from "$lib/Pagination.svelte";

    export let data;

    const authHeader = { "Authorization": `Bearer ${data.authToken}`};
    
    let listCurrentPage = 1;

    $: searchPromise = (search)(listCurrentPage)

    async function search(currentPage) {
        const res = await fetchServer(`products/negativeprice?page=${currentPage}`, {
            headers: authHeader
        });
        const result = await res.json();

        const returnData = {
            payload: [],
            pagination: {
                totalPages: 1,
                page: 1,
            }
        }

        if (result.payload) {
            returnData.payload = result.payload;
            returnData.pagination = result.pagination;
        };

        if (!result.payload && listCurrentPage > 1) listCurrentPage -= 1;

        return returnData;
    }
</script>

<div class="container-fluid p-3">
    <h1 class="content-title mb-3">Price Correction</h1>
    {#await searchPromise}
        <h3>Loading...</h3>
    {:then { payload, pagination}} 
        {#if payload.length > 0}
            <table class="table table-hover">
                <thead>
                    <tr>
                        <th>Name</th>
                        <th>Price</th>
                        <th>Cost</th>
                        <th>Margin</th>
                    </tr>
                </thead>
                <tbody>
                    {#each payload as data, i}
                        <tr>
                            <th><a href="/products/{data.id.toString()}">{data.name}</a></th>
                            <th>{toStringDelimit(data.price)}</th>
                            <th>{toStringDelimit(data.cost)}</th>
                            <th>{@html displayMargin(data.price, data.cost)}</th>
                        </tr>
                    {/each}
                </tbody>
            </table>
            <Pagination
                on:prevPage={() => listCurrentPage -= 1}
                on:nextPage={() => listCurrentPage += 1}
                totalPages={pagination.totalPages}
                currentPage={pagination.page}
            />
        {:else if payload.length == 0}
            <h1 class="text-center mt-5 mb-5">No products yet.</h1>
        {:else}
            <h1 class="text-center mt-5 mb-5">Loading...</h1>
        {/if}
    {/await}
</div>