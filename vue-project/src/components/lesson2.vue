<template>
    <div class="bubble">
        <p>{{ me }}</p>
        <button @click="handle">changeMe</button>
    </div>
    <div class="bubble">
        <input type="text" @input="handleInputChange" placeholder="Enter text">
        <p>Your text: {{ userInput }}</p>
    </div>
    <div class="bubble">
        <p>{{ listOfUsers }}</p>
        <button @click="filterArray">show users who are less than 28</button>
    </div>
    <div class="bubble">
        <button @click="handleMessage">change message in component</button>
        <compToChange :msg="messageToComponent" />
    </div>
    <div class="bubble">
        <form @submit.prevent="validateForm">
            <div class="grid">
                <input type="text" v-model="inputForm" placeholder="Enter login">
                <span class="error" v-if="errors.name">{{ errors.name }}</span>
                <button @click="submit">send to verify</button>
            </div>    
        </form>
    </div>
    <div class="bubble">
        <input type="number" v-model="numberOne">
        <input type="number" v-model="numberTwo">
        <p>sum of your 2 numbers is: {{ getSum }}</p>
    </div>
    <div class="bubble">
        <p>List of users filtered by age bigger than 25: {{ filterListOfObjects }}</p>
    </div>
    <div class="bubble">
        <button @click="fillPlaylist">Fill playlist</button>
        <ul>
            <li v-for="(item, index) in listOfSongs" :key="index">
                {{ item }}
            </li>
        </ul>
    </div>
    <div class="bubble">
        <p>starting number: {{ forTwoComputed }}</p>
        <p>first property does +3 on starting number, so it gets like this: {{ firstComputedProperty }}</p>
        <p>second property does first property and additionally does +4 so it gets like this: {{ secondComputedProperty }}</p>
    </div>
    <div class="bubble">
        <input v-model="searchQuery" type="text" placeholder="Введіть запит для пошуку" />
        <p>Результат пошуку:</p>
        <ul>
            <li v-for="item in results" :key="item.id">{{ item.name }}</li>
        </ul>
    </div>
    <div class="bubble">
        <p>{{ immediateVariable }}</p>
    </div>
</template>

<script type="module">
import compToChange from './componentToChange.vue'

// 9. Створіть computed property, яке комбінує дані відповіді від HTTP запиту та з рекативною змінною.
async function fetchSomeData(search) {
    const url = `https://itunes.apple.com/search?term=${search}`;
    const response = await fetch(url);
    const data = await response.json();
    console.log(data)
    return data;
}

export default {
    name: 'lesson2',
    components: {
        compToChange
    },
    data () {
        return ({ 
            me: {
                name: 'unknown',
                age: 'unknown'
            },
            userInput: '',
            messageToComponent: 'Message to second component that needs to be changed',
            inputForm: '',
            numberOne: 0,
            numberTwo: 0,
            errors: {},
            listOfUsers: [
                {name: 'Jane', age: 30},
                {name: 'John', age: 20},
                {name: 'Kein', age: 28},
                {name: 'Keit', age: 25},
                {name: 'Donald', age: 50},
            ],
            listOfSongs: [

            ],
            forTwoComputed: 5,
            results: [],
            searchQuery: '',
            immediateVariable: 'this message you should see on both page and console',
        })
    },
    watch: {
        // 11. Використовуйте watcher для відстеження змін у реактивному об’єкті та виведення повідомлення про це.
        me(newValue, oldValue)
        {
            console.log(newValue, oldValue)
        },
        // 12. Використовуйте watcher для відстеження змін однієї властивості у реактивному об’єкті та виведення повідомлення про це.
        'me.name'(newValue, oldValue)
        {
            console.log(newValue, oldValue)
        },
        'me.age'(newValue, oldValue)
        {
            console.log(newValue, oldValue)
        },
        // 13. Використовуйте watcher для відправлення API запитів при зміні реактивних даних. 
        async searchQuery(newQuery) {
            if (newQuery.trim()) {
                    await fetchSomeData(newQuery)
                        .then(data => {
                            data.results.map(result => {
                                let playlistLength = this.results.length;
                                let currentTrack = 
                                {
                                    name: result.trackName+' (preview)',
                                    author: result.artistName,
                                    id: playlistLength,
                                }
                                if (playlistLength < 10)
                                {
                                    this.results.push(currentTrack);
                                }
                            })
                        })
                    .catch(error => console.log(`This error occured: ${error}`))                
            } else {
                this.results = [];
            }
        },
        // 14. Створіть watcher, який реагує на зміни в computed property.
        getSum(newValue, oldValue){
            console.log(newValue, oldValue)
        },
        // 15. Використовуйте deep опцію для відстеження властивостей внутрішніх об’єктів.
        listOfSongs: {
            handler(newValue){
                console.log(newValue);
            },
            deep: true
        },
        // 16. Використовуйте опцію immediate для виклику watcher при ініціалізації компонента.
        immediateVariable: {
            handler(newValue){
                console.log(newValue)
            },
            immediate: true
        }
    },
    methods: {
        // 1. Створіть реактивний об'єкт і виведіть його властивості в шаблон. Змініть властивість об'єкта і спостерігайте за змінами в DOM.
        // 2. Створіть метод, який буде викликатися при кліку на кнопку і змінювати деякі дані в інстансі
        handle()
        {
            this.me = {
                name: 'Nikolai',
                age: 20
            }
        },
        // 3. Створіть метод, який приймає подію і використовує ії для зміни даних інстанса.
        handleInputChange(event)
        {
            this.userInput = event.target.value
        },
        // 4. Створіть метод для фільтрації масиву об'єктів за деяким критерієм.
        filterArray()
        {
            return this.listOfUsers = this.listOfUsers.filter(data => data.age < 28)
        },
        // 5. Використайте метод для обробки події кліку, який змінює стан інших компонентів.
        handleMessage(event)
        {
            this.messageToComponent = 'it was changed, now it`s different message!'
        },
        // 6. Створіть метод, який буде перевіряти введені дані у формі на відповідність деяким правилам.
        validateForm()
        {
            if (this.inputForm.length < 3){
                this.errors.name = 'This string must be longer that 3 symobls'
            } else {
                this.errors.name = '';
            }
        },
        
    },
    computed: {
        // 7. Створіть обчислювальну властивість для виведення даних, обчислених на основі інших реактивних даних.
        getSum() {
            return this.numberOne + this.numberTwo;
        },
        // 8. Створіть computed property для фільтрації списку об'єктів.
        filterListOfObjects() {
            return this.listOfUsers.filter(user => user.age > 25) 
        },
        // 9. Створіть computed property, яке комбінує дані відповіді від HTTP запиту та з рекативною змінною.
        fillPlaylist() {
            fetchSomeData('queen')
                .then(data => {
                    data.results.map(result => {
                        let playlistLength = this.listOfSongs.length;
                        let currentTrack = 
                        {
                            name: result.trackName+' (preview)',
                            author: result.artistName,
                            id: playlistLength,
                        }
                        if (playlistLength < 10)
                        {
                            this.listOfSongs.push(currentTrack);
                        }
                    })
                })
                .catch(error => console.log(`This error occured: ${error}`))
        },
        // 10. Створіть кілька computed properties, які залежать одне від одного.
        firstComputedProperty(){
            return this.forTwoComputed+3;
        },
        secondComputedProperty(){
            return this.firstComputedProperty+4;
        },
    }
}

</script>
