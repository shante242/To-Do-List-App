# Formal Outline Report: To-Do List App

## I. Project Description

### Purpose
The To-Do List App makes it simple for users to add, manage, and finish tasks, which aids in maintaining organization. Its straightforward, easy-to-use interface is intended to boost productivity and assist users in keeping track of their daily responsibilities.

### Intended Audience
The program is designed for everyone who needs assistance with task organization, including professionals, students, and regular users who want to keep track of their everyday activities.

---

## II. Problem Addressing

### Problem Statement
Many people have trouble planning and managing their workload, which causes them to miss deadlines and become more stressed.

### App’s Solution
The To-Do List App provides a digital task management solution. It offers an intuitive interface for creating tasks, assigning due dates, and marking them as finished in real time across several devices.

---

## III. Platform

### Platforms of Interest
- iOS
- Android

### Cross-Platform Approach
The program will be created as a cross-platform application using a shared codebase to ensure accessibility to a wide audience without requiring separate development for iOS and Android.

---

## IV. Front-End and Back-End Support

### Front-End Technology
- React Native will be used to build the application's user interface.

### Back-End Technology
- Firebase Realtime Database will store tasks, user information, and support device synchronization.

---

## V. Functionality

### Core Features
 Add, edit, and delete tasks  
 Organize tasks using category tags (e.g., personal, work)  
 Set due dates and push notification reminders  
 Mark tasks as completed and view task status

### Additional Features
- **Cloud Syncing**: Firebase allows task synchronization across multiple devices  
- **Task Search**: Locate tasks by keyword or category  
- **Dark Mode**: Offers an optional dark interface for user comfort

---

## VI. Design (Wireframes)

### User Interface (UI)
The design is straightforward and understated.

### Key Screens
- **Dashboard**: Displays tasks by due date and importance  
- **Task Creation Screen**: Allows users to enter title, category, priority, and due date  
- **Task Detail Screen**: Users can update or mark tasks as finished

### User Experience (UX)
Smooth navigation and task management are enabled by a bottom navigation bar, which provides quick access to the dashboard, task creation, and settings.

---

## VII. Testing

### Unit Testing
- Individual features such as task creation and deletion will be tested for quality assurance.

### Integration Testing
- Ensures seamless communication between Firebase and the app’s front end.

### User Acceptance Testing (UAT)
- User feedback will be collected to improve the app.

### Issue Tracking
- GitHub Issues will be used to monitor bugs and development problems systematically.

---

## VIII. Timeline

### Milestones
- **Phase 1** – Week 1: Wireframe creation and planning  
- **Phase 2** – Weeks 2–5: App development  
- **Phase 3** – Week 6: Testing and bug fixes  
- **Phase 4** – Week 7: Final updates and implementation  

### Due Date
- The completed application will be available by the conclusion of the term.

---

## IX. Budget

### Backend Services
- Firebase Free Tier will be utilized.

### Design Tools
- Minimal development tools like Figma will be used.

### Personnel Costs
- Additional design help may be contracted with a $500 budget estimate.

---

## X. Risk Management

### Identified Risks
- Development delays due to unforeseen challenges  
- Technical difficulties with cross-platform compatibility using React Native or Firebase

### Mitigation Strategies
- Conducting regular development reviews  
- Early testing of Firebase integration  
- Allocating extra time for bug fixes and debugging

---

## XI. Conclusion

### Summary
The To-Do List App is designed to provide a simple yet effective solution for task management. It enhances productivity, reduces stress, and supports daily planning through an intuitive, cross-platform interface that includes the necessary tools for task tracking and organization.




import React, { useState, useEffect } from 'react';
import { View, Text, TextInput, Button, FlatList, StyleSheet, TouchableOpacity } from 'react-native';
import firestore from '@react-native-firebase/firestore';

export default function TodoApp() {
  const [task, setTask] = useState('');
  const [tasks, setTasks] = useState([]);


  useEffect(() => {
    const unsubscribe = firestore()
      .collection('tasks')
      .orderBy('createdAt', 'desc')
      .onSnapshot(snapshot => {
        const newTasks = snapshot.docs.map(doc => ({
          id: doc.id,
          ...doc.data(),
        }));
        setTasks(newTasks);
      });

    return () => unsubscribe();
  }, []);


  const addTask = () => {
    if (task.trim()) {
      firestore().collection('tasks').add({
        title: task,
        completed: false,
        createdAt: firestore.FieldValue.serverTimestamp(),
      });
      setTask('');
    }
  };

  // Toggle task completion
  const toggleComplete = (id, currentStatus) => {
    firestore().collection('tasks').doc(id).update({
      completed: !currentStatus,
    });
  };

  // Delete a task
  const deleteTask = (id) => {
    firestore().collection('tasks').doc(id).delete();
  };

  const renderItem = ({ item }) => (
    <View style={styles.taskItem}>
      <TouchableOpacity onPress={() => toggleComplete(item.id, item.completed)}>
        <Text style={[styles.taskText, item.completed && styles.completedText]}>{item.title}</Text>
      </TouchableOpacity>
      <Button title="Delete" onPress={() => deleteTask(item.id)} />
    </View>
  );

  return (
    <View style={styles.container}>
      <Text style={styles.heading}>To-Do List</Text>
      <TextInput
        style={styles.input}
        placeholder="Add a task..."
        value={task}
        onChangeText={setTask}
      />
      <Button title="Add Task" onPress={addTask} />
      <FlatList
        data={tasks}
        keyExtractor={item => item.id}
        renderItem={renderItem}
        style={styles.taskList}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: '#f5f5f5',
  },
  heading: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  input: {
    borderWidth: 1,
    borderColor: '#ccc',
    padding: 10,
    marginBottom: 10,
    borderRadius: 5,
  },
  taskList: {
    marginTop: 20,
  },
  taskItem: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    backgroundColor: '#fff',
    padding: 15,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
    borderRadius: 5,
    marginBottom: 10,
  },
  taskText: {
    fontSize: 16,
  },
  completedText: {
    textDecorationLine: 'line-through',
    color: '#888',
  },
});
