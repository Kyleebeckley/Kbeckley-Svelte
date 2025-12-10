<script>
  import { onMount } from "svelte";

    let todoItem = $state('');
    let todoCategory = $state('');
    let todoList = $state([]);
    let highList = $state([]);
    let lowList = $state([]);

    onMount(() => {
        let storedList = localStorage.getItem("storedList");
        if (storedList) {
            todoList = (JSON.parse(storedList));
        }
        sortLists();
    })

    function updateList() {
        return localStorage.setItem("storedList", JSON.stringify(todoList));
    }
    function sortLists() {
        highList = todoList.filter(item => item.category == "high");
        lowList = todoList.filter(item => item.category == "low");
    }

    function addItem(event) {
        event.preventDefault();
        if (todoItem.trim().length === 0){
            return;
        }

        todoList = [...todoList,{
            text: todoItem,
            done: false,
            category: todoCategory
        }];
        updateList();
        sortLists();
        todoItem = '';
    }

    function removeItem(index) {
        todoList.splice(index,1);
        updateList();
        sortLists();
    }
</script>


<div class="left-side">
    <h1>To Do List</h1>

    <form onsubmit={addItem}>
        <input type="text" bind:value={todoItem}>
        <div>
            <input type="radio" id="high"
            bind:group={todoCategory} value={"high"}/>
            <label for="high">High Priority</label>
        </div>
        <div>
            <input type="radio" id="low"
            bind:group={todoCategory} value={"low"}/>
            <label for="low">Low Priority</label>
        </div>
        <button type="submit">Add</button>
    </form>
</div>


<div class ="todo-list">
    <h2>High Priority</h2>
    <ul>
        {#each highList as item, index}
            <li>
                <input type="checkbox" bind:checked={item.done} onchange= {updateList}>
                <span class:done={item.done}>{item.text}</span>
                <button type="button" onclick={() => removeItem(index)}>x</button>
            </li>
        {/each}
    </ul>
</div>

<div class ="todo-list">
    <h2>Low Priority</h2>
    <ul>
        {#each lowList as item, index}
            <li>
                <input type="checkbox" bind:checked={item.done} onchange= {updateList}>
                <span class:done={item.done}>{item.text}</span>
                <button type="button" onclick={() => removeItem(index)}>x</button>
            </li>
        {/each}
    </ul>
</div>