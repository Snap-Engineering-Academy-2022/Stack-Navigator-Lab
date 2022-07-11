# Stack Navigator Lab

cmd f
"thursday"
"lab"
update detail / summary / modal tags to work

<aside>
💡 So just as a recap, we talked through [drawer navigators](https://www.notion.so/Day-4-July-15-2021-Firestore-Navigation-9f2f065c20394936b3b829ce154e273b) and [tab navigators](https://www.notion.so/Day-4-July-15-2021-Firestore-Navigation-9f2f065c20394936b3b829ce154e273b) last Thursday. To get more familiar with them, you can go back to the lab materials at a later time!

</aside>

## 0. Before we start, update your code.

- `firebase.js` — we're switching to a class-wide account for Firebase. Make sure you change your config.
    
    ```jsx
    import firebase from "@firebase/app";
    import "@firebase/auth";
    import "@firebase/firestore";
    
    // Your web app's Firebase configuration, which you copy-pasted from Step 6
    const firebaseConfig = {
      apiKey: "AIzaSyC7CQwBSzjC_tlEiMd2Mc8Sh9Fb_Cwc1p8",
      authDomain: "chapsnat-3f4f7.firebaseapp.com",
      projectId: "chapsnat-3f4f7",
      storageBucket: "chapsnat-3f4f7.appspot.com",
      messagingSenderId: "239440555368",
      appId: "1:239440555368:web:d7d431a3733e778d273add",
      measurementId: "G-W4Y70B8JL2",
    };
    // Initialize Firebase
    
    firebase.initializeApp(firebaseConfig);
    let firestore = firebase.firestore();
    
    export default firestore;
    ```
    
- `App.js` — this is the completed file, if you finished the Firebase lab from last Thursday.
    
    ```jsx
    //App.js
    import React, { useState, useCallback, useEffect } from "react";
    import { GiftedChat } from "react-native-gifted-chat";
    import db from "./firebase";
    import firebase from "firebase/app";
    
    export default function App() {
      const [messages, setMessages] = useState([]);
    
      useEffect(() => {
        let unsubscribeFromNewSnapshots = db
          .collection("Chats")
          .doc("myfirstchat")
          .onSnapshot((snapshot) => {
            console.log("New Snapshot!");
            setMessages(snapshot.data().messages);
          });
    
        return function cleanupBeforeUnmounting() {
          unsubscribeFromNewSnapshots();
        };
      }, []);
    
      const onSend = useCallback((messages = []) => {
        db.collection("Chats")
          .doc("myfirstchat")
          .update({
            // arrayUnion appends the message to the existing array
            messages: firebase.firestore.FieldValue.arrayUnion(messages[0]),
          });
        setMessages((previousMessages) =>
          GiftedChat.append(previousMessages, messages)
        );
      }, []);
    
      return (
        <GiftedChat
          messages={messages}
          onSend={(messages) => onSend(messages)}
          user={{
            // current "blue bubble" user
            _id: "1",
            name: "Ashwin",
            avatar: "https://placeimg.com/140/140/any",
          }}
          inverted={true}
          showUserAvatar={true}
          renderUsernameOnMessage={true}
        />
      );
    }
    ```
    

If you want to open up our class Firebase in the console, 

```jsx
Username: chapsnatsea@gmail.com
Password: snapacademies
```

## 1. In your groups, explore stack navigators.

[StackNavigator](https://snack.expo.io/@jennyhansolo/stacknavigator)

- **✨💪 Check out the instructions here. ✨💪**
    - **[✨💪 ACTION ITEM ☑️  ]** The link between `ScreenTwo` and `ScreenToImplement` is broken!
        - You will need to change `line 8` of `screens/ScreenTwo.js`
        - Check line 18 - 20 within `App.js` to see what you should add in `line 8`.
    - **[✨💬  DISCUSS]**  Find where these Stack Navigator-specific functions are being used throughout the app! What are they doing?
        - navigate('RouteName')
            - If screen is in stack, navigates to screen; else pushes to stack
        - .goBack()
            - A way to go back from within the component
        - .popToTop()
            - Goes back to the first screen in the stack
        - push('RouteName')
            - Pushes route on top of stack again, even if it is already in the stack
            - Different from navigate(‘RouteName’) which will go back to where the route was originally in the stack if it's already in the stack
            - [https://stackoverflow.com/questions/51090135/react-navigation-v2-difference-between-navigation-push-and-navigation-navigate](https://stackoverflow.com/questions/51090135/react-navigation-v2-difference-between-navigation-push-and-navigation-navigate)
    - **[✨💬  ACTION ITEM]** Now let's discuss how to pass information from one screen to the other. They're very similar to props....
        - You can think of them as props passed in with Navigation specifically.
            
            ```jsx
            navigation.navigate('RouteName', { /* params go here */ })
            
            //Example: 
            navigation.navigate('Opinions', {myParam: ‘Dogs are cool’});
            ```
            
        - When you go to a specific screen, you can access the Navigation Props under the `route` object:
            
            ```jsx
            //Example
            function RouteName({ route, navigation }){
            	const { myParam } = route.params;
            }
            ```
            
        - In `line 8` of `ScreenToImplement.js`, pass in `route.params.message` instead of 'blahblahblah'!
        - Then go to `line20` of `App.js` and change the `message` to something new!
    - **[✨💪 ACTION ITEM ☑️ ]** Let's add some styling! To do that, let's first try the screenOptions prop to `Stack.Navigator` itself, in `line 16` of `App.js`. This will change the header for every single screen.
        - Change the background color and header title color!
        
        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e9b7e57c-951c-4c28-8f8c-2baf34c5f8f0/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e9b7e57c-951c-4c28-8f8c-2baf34c5f8f0/Untitled.png)
        
    
    ```jsx
    screenOptions= {{
              headerStyle: {
                backgroundColor: '#87CEEB',
              },
              headerTitleStyle: {
                color: 'white',
                fontWeight: 'bold',
              },
            }}
    ```
    
    - **[✨💪 ACTION ITEM ☑️ ]Now... what if we just wanted to change the header for a particular screen. To do that, let's first try the `options` prop to `ScreenOne`**
    
    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7dcd7389-4d9b-4864-82f5-002a9a7995b0/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7dcd7389-4d9b-4864-82f5-002a9a7995b0/Untitled.png)
    
    ```jsx
    options={{
                headerRight: () => (
                  <Button
                    title="Alert"
                    onPress={() => alert('Button Pressed')}
                  />
                )
              }}
    ```
    

## 2. Install React Navigation packages

**✨💪 In the terminal INSIDE your CHAPSNAT project repo, type the following ✨💪** 

```jsx
yarn add @react-navigation/native

yarn add @react-navigation/stack

yarn add @react-navigation/bottom-tabs

yarn add react-native-screens

yarn add react-native-gesture-handler
```

**👀 Are they in the `package.json` file? If so, you can move on to step 3! 👀**

## 3. Now we're going to add navigation to our own chapsnat repo.

### **a. First things first, what does our app wireflow look like again?**

Well, there are two separate stacks (one green, one red). Depending on whether you are logged in or not, you'll get one of the two stacks.  The green stack also has a bottom tab navigator INSIDE its stack! Neato. 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/410c264b-4af8-4ff5-a9ba-3d603537faf6/IMG_761FF9B3C9FC-1.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/410c264b-4af8-4ff5-a9ba-3d603537faf6/IMG_761FF9B3C9FC-1.jpeg)

This looks like a lot! So let's start with a simpler example.

### **b. Let's just implement a single stack for now: the Chats List/Home screen and Single Chat screen.**

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/17d70c6a-4908-4e9d-ba11-01e892ac0396/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/17d70c6a-4908-4e9d-ba11-01e892ac0396/Untitled.png)

✨💪 First, make a `screens` folder in your repo. Then create a ChatScreen.js file and a HomeScreen.js file within that folder. Update your code by copying paste this: ✨💪 

- `App.js`
    
    ```jsx
    import React from "react";
    import { StyleSheet } from "react-native";
    import { NavigationContainer } from "@react-navigation/native";
    import { createStackNavigator } from "@react-navigation/stack";
    import ChatScreen from "./screens/ChatScreen";
    import HomeScreen from "./screens/HomeScreen";
    
    const Stack = createStackNavigator();
    
    function App() {
      return (
        <NavigationContainer>
          <Stack.Navigator initialRouteName="Home">
            <Stack.Screen name="Home" component={HomeScreen} />
            <Stack.Screen name="Chat" component={ChatScreen} />
          </Stack.Navigator>
        </NavigationContainer>
      );
    }
    
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        backgroundColor: "#fff",
      },
      item: {
        padding: 10,
        fontSize: 18,
        height: 44,
      },
    });
    
    export default App;
    ```
    
- `screens/ChatScreen.js`
    
    ```jsx
    import React, { useState, useCallback, useEffect } from "react";
    import { GiftedChat } from "react-native-gifted-chat";
    import db from "./firebase";
    import firebase from "firebase/app";
    
    export default function ChatScreen({ navigation }) {
      const [messages, setMessages] = useState([]);
    
      useEffect(() => {
        let unsubscribeFromNewSnapshots = db
          .collection("Chats")
          .doc("myfirstchat")
          .onSnapshot((snapshot) => {
            console.log("New Snapshot!");
            setMessages(snapshot.data().messages);
          });
    
        return function cleanupBeforeUnmounting() {
          unsubscribeFromNewSnapshots();
        };
      }, []);
    
      const onSend = useCallback((messages = []) => {
        db.collection("Chats")
          .doc("myfirstchat")
          .update({
            // arrayUnion appends the message to the existing array
            messages: firebase.firestore.FieldValue.arrayUnion(messages[0]),
          });
        setMessages((previousMessages) =>
          GiftedChat.append(previousMessages, messages)
        );
      }, []);
    
      return (
        <GiftedChat
          messages={messages}
          onSend={(messages) => onSend(messages)}
          user={{
            // current "blue bubble" user
            _id: "1",
            name: "Ashwin",
            avatar: "https://placeimg.com/140/140/any",
          }}
          inverted={true}
          showUserAvatar={true}
          renderUsernameOnMessage={true}
        />
      );
    }
    ```
    
- `screens/HomeScreen.js`
    
    ```jsx
    import React, { useState, useEffect } from "react";
    import { FlatList, Text, View, TouchableOpacity, StyleSheet } from "react-native";
    import db from "./firebase";
    
    export default function HomeScreen({ navigation }) {
      const [chatList, setChatList] = useState([]);
    
      useEffect(() => {
        let chatsRef = db.collection("Chats");
        chatsRef.get().then((querySnapshot) => {
          let newChatList = [];
          querySnapshot.forEach((doc) => {
            let newChat = { ...doc.data() };
            newChat.id = doc.id;
            newChatList.push(newChat);
            console.log(newChatList);
          });
          setChatList(newChatList);
        });
      }, []);
    
      return (
        <View style={styles.container}>
          <FlatList
            data={chatList}
            renderItem={({ item }) => (
              <TouchableOpacity
                onPress={() => navigation.navigate("Chat")}
              >
                <Text style={styles.item}>{item.id}</Text>
              </TouchableOpacity>
            )}
          />
        </View>
      );
    }
    
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        backgroundColor: "#fff",
      },
      item: {
        padding: 10,
        fontSize: 18,
        height: 44,
      },
    });
    ```
    

### **c. When you run this code, there are 5 action items to be addressed!  🐛🐛🐛**

- 🐛 #1: Bug – "Module can't be found"
    - In both `HomeScreen.js` and `ChatScreen.js`, we need to change the import statement `import db from "./firebase";` slightly because we nested these files inside a folder. Can you figure out how?
        
        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9d570319-550b-43ee-bd18-929a071aa32b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9d570319-550b-43ee-bd18-929a071aa32b/Untitled.png)
        
        - Solution to check
            
            `import db from "../firebase";`
            
- 👀 #2: Reading Code – Chat Collection Query Snapshot
    
    What is this `useEffect` doing? It's being called right as your screen loads. 
    
    - 💪**Go through each line of this function with your partners and make sure you understand what is going on! ⁉️If you get stuck, ask a teaching team member to join you.⁉️**
        - Some references you might find helpful:
            - [https://firebase.google.com/docs/firestore/query-data/queries](https://firebase.google.com/docs/firestore/query-data/queries)
            - [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
            - [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
    
    ```jsx
    useEffect(() => {
      let chatsRef = db.collection("Chats");
      chatsRef.get().then((querySnapshot) => {
        let newChatList = [];
        querySnapshot.forEach((doc) => {
          let newChat = { ...doc.data() };
          newChat.id = doc.id;
          newChatList.push(newChat);
          console.log(newChatList);
        });
        setChatList(newChatList);
      });
    }, []);
    ```
    
- 👀 #3: Reading Code – <FlatList>
    
    ```jsx
    <FlatList
      data={chatList}
      renderItem={({ item }) => (
        <TouchableOpacity
          onPress={() => navigation.navigate("Chat")}
        >
          <Text style={styles.item}>{item.id}</Text>
        </TouchableOpacity>
      )}
    />
    ```
    
    As you learned in part 4, `chatList` is a state variable that contains a list of chats! 
    
    - We want to display these as a clickable list on the screen... but the `<ul>`  and `<ol>` elements don't exist on mobile, they only exist as web components!
    - Do not fear! `<FlatList>` is here to save the day.
    - 💪**Check out the documentation for <FlatList> [here](https://reactnative.dev/docs/flatlist). Then discuss with your partners:**
        - **What does the `data` prop take in as input?**
        - **What does the `renderItem` prop take in as input?**
    - Add styling to the TouchableOpacity within the FlatList to give some styles to the list!
- **🐛 #4: Bug – ChatScreen is hard-coded and only loads messages from `myfirstchat` even if you click on `SEA Group Chat`!**
    - Using what you learned about `route.params` earlier (and this document about [https://reactnavigation.org/docs/params](https://reactnavigation.org/docs/params)!), can you change `HomeScreen.js` and `ChatScreen.js` so you pass in a chat id to `ChatScreen.js`
        - Remember, you can use `route.params`, which are kind of like props but passed in with Navigation specifically.
            
            ```jsx
            navigation.navigate('RouteName', { /* params go here */ })
            
            //Example: 
            navigation.navigate('Chat', {chatname: item.id});
            ```
            
        - Start with line 34/28 on `HomeScreen.js`
        - When you go to a specific screen, you can access the Navigation Props under the `route` object:
            
            ```jsx
            //Example
            function RouteName({ route, navigation }){
            	const { myParam } = route.params;
            }
            ```
            
        - Then edit line 6, 12, and 25 of `ChatScreen.js`
        - Solution to check
            
            Your completed code should look like this: 
            
            [https://github.com/Snap-Engineering-Academy-2021/chapsnat/blob/nav/screens/HomeScreen.js](https://github.com/Snap-Engineering-Academy-2021/chapsnat/blob/nav/screens/HomeScreen.js)
            
            [https://github.com/Snap-Engineering-Academy-2021/chapsnat/blob/nav/screens/ChatScreen.js](https://github.com/Snap-Engineering-Academy-2021/chapsnat/blob/nav/screens/ChatScreen.js)
            
- 🐛 #5: Bug – Invalid Date.
    - We'll address this as a whole class. You can skip this for now!
        
        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/72651bf4-2da7-4d30-ba99-b014eab8a73d/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/72651bf4-2da7-4d30-ba99-b014eab8a73d/Untitled.png)
        

### d. Commit and push what you have to your own repo.

### e. Finished early? Add styling to the HomeScreen so it looks like the Snapchat Chat List screen!