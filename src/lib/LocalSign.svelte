<script lang="ts">
    import { ethers } from 'ethers';
    import { fly } from 'svelte/transition';

    export let web3Props: Web3Props;
    $: nonce = Math.floor(Math.random() * 1000000);
    $: signature = '';
    let valid: boolean | null = null;
    $: address = '';
    $: signed = false;
    let expectedAddr: string = 'Select Address';
    const notYourAddr = '0x0000000000000000000000000000000000000000';
	let managingProfiles = false;
	let selectingNewService = false;
	let monitoringService = false;
	let calculatingBreaches = false;
	let calculatingPenalty = false;



    async function sign() {
        nonce = Math.floor(Math.random() * 1000000);
        signature = await web3Props.signer.signMessage(`Signing one-time nonce: ${nonce}`);
        signed = true;
        valid = null; // Reset valid to ensure verification happens again
    }
	function generateDigitalIdentityNumber() {
  	// This is a placeholder. In a real-world scenario, you'd want this to be generated and validated by your backend.
  	return Date.now(); // Using the current timestamp as a simple example
	}
    function verify() {
    address = ethers.utils.verifyMessage(`Signing one-time nonce: ${nonce}`, signature);
    valid = address === expectedAddr;
    if (valid) {
        // Generate the digital identity number
        const digitalIdentityNumber = generateDigitalIdentityNumber();
        // Display the success prompt with the digital identity number
        alert(`Transaction successful! You have been registered.\n\nYour Digital Identity Number is: ${digitalIdentityNumber}. Please save this information.`);
    }
}

    async function reset() {
        nonce = Math.floor(Math.random() * 1000000);
        signature = '';
        valid = null;
        address = '';
        signed = false;
        web3Props.account = await web3Props.signer.getAddress();
    }

	// Add these new functions
    function manageProfiles() {
        managingProfiles = true;
        
        // Possibly add additional logic to setup the management view
    }

    function manageConsumerProfile() {
        // Add logic to manage the consumer profile here
        // This could involve setting additional state variables, navigating to a new route, or displaying a form
    }

    function manageProviderProfile() {
        // Add logic to manage the provider profile here
        // Similar to above, this would be specific to your application's requirements
    }
	function selectNewService() {
    selectingNewService = true;
    // Reset other states if needed
	}

	function monitorExistingService() {
    	monitoringService = true;
    	// Reset other states if needed
	}

	function calculateBreaches() {
    	calculatingBreaches = true;
    	// Reset other states if needed
	}

	function calculatePenalty() {
    	calculatingPenalty = true;
    	// Reset other states if needed
}




	async function submitProof() {
    // Ensure that the user is using their registered address
    const currentAddress = await web3Props.signer.getAddress();
    if (currentAddress !== expectedAddr) {
        alert('Proof cannot be submitted because the address is not the registered address.');
        return;
    }
  
    // Request the user to sign a confirmation message for proof submission
    try {
        const proofSubmissionSignature = await web3Props.signer.signMessage('Submit proof confirmation');
        // You would typically send this signature to your backend for verification
        // Here, we will just simulate the successful verification
        console.log('Proof submission confirmed:', proofSubmissionSignature);
      
        // Proceed with proof submission
        alert('Proof submitted successfully!');
        // In a real application, this might involve sending a transaction or uploading a file
    } catch (error) {
    if (error instanceof Error) {
        // Now TypeScript knows that error is of type Error, so error.message is safe to access
        alert(`Proof submission failed: ${error.message}`);
    } else {
        // Handle cases where the caught error is not an instance of Error
        alert('Proof submission failed due to an unknown error.');
    }
}

}


</script>

<div class="grid place-content-center pt-11">
    {#if !signed}
        <div
            class="card bg-base-100 shadow-xl max-w-xl"
            in:fly={{ duration: 200, x: 100 }}
            out:fly={{ duration: 100, x: -100 }}
        >
            <figure><img src="https://placeimg.com/400/225/arch" alt="Random" /></figure>
            <div class="card-body">
                <h2 class="card-title">Welcome to the Marketplace DApp!</h2>
                <p>For verfied access only reasons, we need to confirm your blockchain address. Please select your address and sign the message to proceed with the registration.</p>
                <select class="select select-primary w-full max-w-xs" bind:value={expectedAddr}>
                    <option disabled>Select Address</option>
                    <option value={web3Props.account}>Current Wallet Address</option>
                    <option value={notYourAddr}>Another Address</option>
                </select>
                <div class="card-actions justify-end">
                    <button
                        class="btn btn-primary"
                        on:click={sign}
                        disabled={expectedAddr === 'Select Address'}
                    >Register Me! </button>
                </div>
            </div>
        </div>
    {/if}
    {#if managingProfiles}
    <div
        class="card bg-base-100 shadow-xl max-w-xl"
        in:fly={{ duration: 200, y: 100 }}
        out:fly={{ duration: 100, y: -100 }}
    >
        <div class="card-body">
            <h2 class="card-title">Manage Profiles</h2>
            <p>Select the profile you would like to manage.</p>
            <div class="card-actions justify-end">
                <button class="btn btn-primary" on:click={manageConsumerProfile}>Manage Consumer Profile</button>
                <button class="btn btn-primary" on:click={manageProviderProfile}>Manage Provider Profile</button>
                <button class="btn btn-primary" on:click={reset}>Home Page</button>
            </div>
        </div>
    </div>
{/if}

{#if signed}
    <div class="card bg-base-100 shadow-xl max-w-xl" in:fly={{ delay: 100, duration: 200, x: 100 }} out:fly={{ duration: 100, x: -100 }}>
        <figure><img src="https://placeimg.com/400/225/arch" alt="Random" /></figure>
        <div class="card-body">
            {#if valid === true}
                <p>Congratulations! Your address has been confirmed. You're now registered and ready to explore.</p>
                <button class="btn btn-primary" on:click={submitProof}>Submit ZKProofs</button>
                <button class="btn btn-primary" on:click={reset}>Explore Marketplace</button>
                <button class="btn btn-primary" on:click={manageProfiles}>Manage Profiles</button>
                <button class="btn btn-primary" on:click={reset}>Request a DID</button>
                <button class="btn btn-primary" on:click={reset}>Manage Tokens</button>
                <button class="btn btn-primary" on:click={reset}>Home Page</button>
            {:else if valid === false}
                <p>The address could not be confirmed. Please ensure you have signed with the correct address.</p>
                <button class="btn btn-primary" on:click={reset}>Try Again</button>
            {:else}
                <p>Signature pending verification...</p>
                <h2 class="card-title">Message Signed:</h2>
                <p>Signing one-time nonce: {nonce}</p>
                <h2 class="card-title">Transaction Completed with Signature:</h2>
                <p class="break-words">{signature}</p>
                <h2 class="card-title">Expected Address:</h2>
                <p>{expectedAddr}</p>
                <h2 class="card-title">Derived Address:</h2>
                <p>{address ? address : 'Verify Digital Signature To Access 👇'}</p>
                <div class="card-actions">
                    <div class="stat">
                        <div class="stat-actions">
                            <button class="btn btn-sm btn-success" on:click={verify} disabled={valid !== null}>Verify Signature</button>
                        </div>
                        <div class="stat-value text-primary">
                            <button class="btn btn-primary" on:click={reset}>Home Page</button>
                        </div>
                    </div>
                </div>
            {/if}
        </div>
    </div>
{/if}
</div>
