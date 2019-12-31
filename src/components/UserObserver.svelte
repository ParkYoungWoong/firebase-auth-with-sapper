<script>
  import { onMount } from 'svelte';
  import { currentUser } from '../store/user';

  onMount(() => {
    firebase.auth().onAuthStateChanged(user => {
      console.log('user:', user)
      if (user) {
        $currentUser = user
      } else {
        $currentUser = null
      }
    });
  });
</script>
