<template>
  <div class="about">
    <h1>This is an about page</h1>
    <input type="text" v-model="newMovieTitle" placeholder="Add a new movie" />
    <button @click="addMovie">Add Movie</button>
    <ul>
      <li v-for="movie in movies" :key="movie">
        {{ movie.title }}
        <button @click="deleteMovie(movie.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script setup>
/* 
Week 35 Setup
setup firebase project
setup vue
npm install firebase + npm install firebase-tools
firebase login (in terminal)
open firebase console and "create a mew project" + "create new database"
Changed the rules so that the database is opened for longer 
Made placeholder collection (data)
Imported firebase  firebaseConfig (api key, and more) 
Imported Firebase functions (initializeApp, getFirestore, collection etc.)
Created input field to add a new movie - STEP 1
Created a list of movies - STEP 2
Created a reference to the movies collection in Firebase - STEP 2.5
Created a function to retrieve a new movie to the list - STEP 3
Created a function to add a new movie to the list - STEP 4
Created a function to delete a movie from the list - STEP 5
Installed dotenv and created a .env file with the firebaseConfig (Security)
*/
import { ref, onMounted } from 'vue'
import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc } from 'firebase/firestore'

// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration (STEP FIREBASE)
const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// Step 1 Create a new movie title and store it in a ref
const newMovieTitle = ref('')

// Step 2 Create a list of movies and store it in a ref
const movies = ref([])

// Step 2.5 Create a reference to the movies collection in Firebase
const moviesFirebaseCollectionRef = 'movies'

const moviesCollection = collection(db, moviesFirebaseCollectionRef);
// Step 3 Create a function to retrieve a new movie to the list
onMounted (() => {
  onSnapshot(moviesCollection, (snapshot) => {
    movies.value = snapshot.docs.map(doc => ({
      id: doc.id,
      ...doc.data() //Spread Operator
      // title: doc.data().title
    }))
  })
})

// Step 4 Create a function to add a new movie to the list
const addMovie = async () => {
  if (newMovieTitle.value.trim() === '') return; // Checks to see if the input is empty, return (stops the function)
  
  await addDoc(moviesCollection, {
    title: newMovieTitle.value
  })

  newMovieTitle.value = ''
}

// Step 5: create a function to delete a movie from the list
const deleteMovie = async (id) => {
  console.log("Deleting movie with id: ", id)
  await deleteDoc(doc(db, moviesFirebaseCollectionRef, id))
}
</script>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}

</style>
