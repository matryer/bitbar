<script>
	import { onMount, createEventDispatcher } from 'svelte'
    import { refreshAllPlugins } from '../rpc.svelte'
    import Button from './Button.svelte'

    // dispatch handles the following events:
    // 		on:change - fired when the values change
    const dispatch = createEventDispatcher()

    import VariableInput from './VariableInput.svelte'

    export let variables = null
    export let values
    export let debounce = 1000
    export let disabled = false

    export let showingTip = false
    export let showShouldRefreshTip = false

    onMount(() => {
        // wait a second before looking for the
        // refresh tip
        setTimeout(() => {
            showingTip = true
        }, 500)
    })

    let _debounceTimer
    function valueDidChange() {
        clearTimeout(_debounceTimer)
        _debounceTimer = setTimeout(() => {
            dispatch('change')
            if (showingTip) {
                showShouldRefreshTip = true
            }
        }, debounce)
    }

    function onRefreshThePluginsClick() {
        showShouldRefreshTip = false
        refreshAllPlugins()
    }

</script>

{#if variables && variables.length && values}
    <div class='p-6 pb-0 bg-white dark:bg-gray-700 dark:bg-opacity-25 bg-opacity-50 border-t border-gray-100 dark:border-gray-600'>
        <table class='table-auto'>
            {#each variables as variable}
                <tr>
                    <td class='py-3 pr-6 text-sm'>
						<label for='{variable.name}'>
							<code>
								{variable.name}
							</code>
						</label>
                    </td>
                    <td class='py-3 pr-6'>
						<VariableInput 
							on:change={valueDidChange}
							values={values}
							variable={variable}
                            disabled={disabled}
						/>
                    </td>
                    <td class='py-3 pr-6 max-w-md text-sm text-gray-500 dark:text-gray-400'>
						{variable.desc}
                    </td>
                </tr>
            {/each}
        </table>
        <p class='pt-3 pb-6 opacity-75 text-sm max-w-sm'>
            <span class='bg-yellow-100 bg-opacity-50 dark:bg-transparent' class:invisible={ !showShouldRefreshTip }>💡 To see your changes, <Button on:click={ onRefreshThePluginsClick }>Refresh the plugins</Button></span>
        </p>
    </div>
{/if}
