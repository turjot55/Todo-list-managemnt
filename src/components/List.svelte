<script>
    import { onMount } from "svelte";

    import { items } from "../stores";

    import TodoApi from "../TodoApi";

    import Item from "./Item.svelte";

    import NewItem from "./NewItem.svelte";
    import { v4 as uuidv4} from "uuid";


    function handleNewItem (e) {
      $items = [
        {
            id: uuidv4(),
            text: e.detail,
            completed: false
        },
        ...$items
      ]
      TodoApi.save($items);
    }
    function handleUpdate (e) {
       const index = $items.findIndex(item => item.id === e.detail.id);
       $items[index] = e.detail;
       TodoApi.save($items);
    }
    function handleDelete (e) {
        $items = $items.filter(item => item.id !== e.detail);
        TodoApi.save($items)
    }

    onMount(async () => {
      //Fetch from api
      $items = await TodoApi.getAll();
    });
</script>

<style>
 .list {
   padding: 15px;
 }

 .list-status {
    margin: 0;
    text-align: center;
    color: antiquewhite;
    font-weight: bold;
    font-size: 1.1em;
 }
</style>

<div class="list">
    <NewItem on:newitem={handleNewItem}/>
    {#each $items as item (item)}
    <Item {...item} on:update={handleUpdate} on:delete={handleDelete}/>
    {:else}
    <p class="list-status">No Items Exist</p>
    {/each}
</div>