<script>
  import { onMount, tick } from 'svelte'
  import { currentUser } from '../store/user';

  onMount(() => {
    if (!$currentUser) {
      createLoginButton();
    }
  });

  function createLoginButton() {
    // FirebaseUI config.
    const uiConfig = {
      signInOptions: [
        firebase.auth.GoogleAuthProvider.PROVIDER_ID
      ],
      callbacks: {
        signInSuccessWithAuthResult: (authResult, redirectUrl) => false
      }
    };

    // Initialize the FirebaseUI Widget using Firebase.
    const ui = firebaseui.auth.AuthUI.getInstance() || new firebaseui.auth.AuthUI(firebase.auth());
    ui.start("#firebaseui-auth-container", uiConfig);
  }

  const signInWithGoogle = async () => {
    await firebase.auth().signInWithPopup(
      new firebase.auth.GoogleAuthProvider()
    );
  };

  const signOut = async () => {
    await firebase.auth().signOut();
    await tick();
    createLoginButton();
  };
</script>

<div class="sign-in-with-google">
  {#if $currentUser}
    <div class="user">
      <div class="user__name">{$currentUser.displayName}</div>
      <div class="user__photo">
        <img
          src={$currentUser.photoURL}
          alt={$currentUser.displayName}
          width="28" />
      </div>
    </div>
    <div
      class="sign-out"
      on:click={signOut}>
      Sign out
    </div>
  {:else}
    <div
      class="sign-in"
      on:click={signInWithGoogle}>
      <div id="firebaseui-auth-container"></div>
    </div>
  {/if}
</div>

<style>
  .sign-in-with-google {
    height: 56px;
    position: absolute;
    top: 0;
    right: 1em;
    display: flex;
    align-items: center;
  }
  .sign-in-with-google .user {
    display: flex;
    align-items: center;
  }
  .sign-in-with-google .user .user__name {
    font-size: 13px;
    font-weight: 700;
    margin-right: 6px;
  }
  .sign-in-with-google .user .user__photo {
    width: 28px;
    height: 28px;
    border: 1px solid lightgray;
    border-radius: 50%;
    overflow: hidden;
    margin-right: 10px;
  }
  .sign-in-with-google .sign-out {
    font-size: 13px;
    cursor: pointer;
  }
  .sign-in-with-google .sign-out:hover {
    text-decoration: underline;
  }

  /* reset firebaseui margin & padding */
  :global(.firebaseui-card-content) {
    padding: 0 !important;
  }
  :global(.firebaseui-idp-list),
  :global(.firebaseui-list-item) {
    margin: 8px 0 !important;
  }
</style>
