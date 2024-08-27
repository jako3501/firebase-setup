<template>
  <div class="about">
    <h1>This is an about page</h1>
    <input type="text" v-model="newMovieTitle" placeholder="Add new movie" />
    <button @click="addMovie">Add Movie</button>
    <ul>
      <li v-for="movie in movies" :key="movie.id">
        Title: {{ movie.title }}
        <button @click="deleteMovie(movie.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc } from 'firebase/firestore';

/* 
Step 0: Setup walkthrough
Setup firebase project
Setup Vue
npm install firebase + npm install firebase-tools
firebase login (in terminal)
Open firebase console and"create  a new project" + "create new database"
Change firebaserules (tab) to change the date
Made placeholder collection (data)
Import firebaseConfig (api key and and more)
Imported firebase functions (initializeApp, getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc)
Imported vue functions (ref, onMounted)
Created input field to add a new movie - (STEP 1)
Created a list of movies - (STEP 2)
Created a function to retrieve new movies to the list - (STEP 3)
Created a reference to the movies collection in Firebase - (STEP 3.5)
Created a function to add a new movie to the list - (STEP 4)
Created a function to delete a movie from the list - (STEP 5)
Installed dotenv : npm install dotenv (for security) (STEP 6)
Created a .env file and added the root folder of the project (STEP 6.5)

*/

// Import the functions you need from the SDKs you need    --- STEP FIREBASE ---
import { initializeApp } from "firebase/app";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID,
  measurementId: import.meta.env.VITE_FIREBASE_MEASUREMENT_IDn
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);



// Step  1: Create a new movie title andstore it in a ref
const newMovieTitle = ref('');

// Step 2: create a list of movies andstore it in a ref
const movies = ref([]);

// Step 3.5: Create a reference to  the movies collection in Firebase
const moviesFirebaseCollectionRef = 'movies';

// Step 3: Create a function to retrieve new movies to the list

const moviesCollection = collection(db, moviesFirebaseCollectionRef);

onMounted(() => {
  onSnapshot(moviesCollection, (snapshot) => {
    movies.value = snapshot.docs.map(doc => ({
      id: doc.id,
      ...doc.data()
      //"...doc.data()" takes all data inside the document. (Called a spread operator)
      // title: doc.data().title
    }))
  })
})

// Step 4: Create a function to add a new movie to the list
const addMovie = async () => {
  if(newMovieTitle.value.trim() === '') return; // Checks if input is empty, return (stop function), .trim removes whitespace(mellemrum)

  await addDoc(moviesCollection, { 
    title: newMovieTitle.value 
  });
  newMovieTitle.value = ''; // Empty the input field after adding a new movie
}

// Step 5: Create a function to delete a movie from the list
const deleteMovie = async (id) => {
  console.log('Deleting movie with id:', id)
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
