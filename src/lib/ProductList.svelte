<script>
    import { goto } from "$app/navigation";
    import { displayMargin } from "./marginCalculator";
    import { toStringDelimit } from "./numbering";

    export let products = [];
</script>

{#if products.length > 0}
    <table class="table table-hover">
        <thead>
            <tr>
                <th><input type="checkbox" name="selectAll" id="selectAll"></th>
                <th>Name</th>
                <th>Price</th>
                <th>Cost</th>
                <th>Margin</th>
                <th>Stock</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            {#each products as product, i}
                <tr>
                    <th><input bind:value={product.selected} type="checkbox" name="select-{i}" id="select-{i}"></th>
                    <th><a href="/products/{product.id.toString()}">{product.name}</a></th>
                    <th>{toStringDelimit(product.price)}</th>
                    <th>{toStringDelimit(product.cost)}</th>
                    <th>{@html displayMargin(product.price, product.cost)}</th>
                    <th><button on:click={() => goto(`/stocks/product/${product.id}`)} type="button" class="btn btn-secondary btn-sm me-2">edit</button><span>{product.stock}</span></th>
                    <td><button on:click={() => goto(`/prices/products/${product.id}`)} type="button" class="btn btn-sm btn-secondary">prices</button></td>
                </tr>
            {/each}
        </tbody>
    </table>
{:else if products.length == 0}
    <h1 class="text-center mt-5 mb-5">No products yet.</h1>
{:else}
    <h1 class="text-center mt-5 mb-5">Loading...</h1>
{/if}