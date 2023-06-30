<script>
	// Import the functions you need from the SDKs you need
	import { initializeApp } from 'firebase/app';
	// TODO: Add SDKs for Firebase products that you want to use
	// https://firebase.google.com/docs/web/setup#available-libraries

	// Your web app's Firebase configuration
	const firebaseConfig = {
		apiKey: 'AIzaSyBz2OFyac4LdNAA8jomtLlpSdBsO1BWyjY',
		authDomain: 'withcenter-test-2.firebaseapp.com',
		databaseURL: 'https://withcenter-test-2-default-rtdb.asia-southeast1.firebasedatabase.app',
		projectId: 'withcenter-test-2',
		storageBucket: 'withcenter-test-2.appspot.com',
		messagingSenderId: '817502397544',
		appId: '1:817502397544:web:c3089782f014d4032487dd'
	};

	// Initialize Firebase
	const app = initializeApp(firebaseConfig);
</script>

<!-- Navigation Bar -->
<main>
	<nav>
		<a href="/">Home</a>
		<a href="/chat">ChatBot</a>
	</nav>
</main>

<slot />
