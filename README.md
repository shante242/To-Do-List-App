# To-Do-List-App
Project Overview

#Purpose
The To-Do List App makes it simple for users to add, manage, and finish tasks, which aids in maintaining organization. Its straightforward, easy-to-use interface is intended to boost productivity and assist users in keeping track of their daily responsibilities.

#Intended audience  
The program is designed for everyone who needs assistance with task organization, including professionals, students, and regular users who want to keep track of their everyday activities.

#Objectives and Goals
Sort tasks into different categories, such as work and personal.
Establish deadlines and reminders.
Check completed tasks and mark them as finished.
Solving Issues

#Problem Adressing 

#Problem Statement
Many people have trouble planning and managing their workload, which causes them to miss deadlines and become more stressed. This app provides a digital task management solution in order to address this problem.

#Apps's Solution
Across several devices, the To-Do List App offers an intuitive interface for creating tasks, assigning due dates, and marking them as finished in real time.

#Platform

#Platforms of Interest: iOS and Android
Without requiring distinct codebases for iOS and Android, the program will be created as a cross-platform application to guarantee accessibility to a wide audience.

#Back-end and front-end Support  

React Native is a front-end technology used to create the application's user interface.
Back-end technologies include Firebase (Realtime Database), which is used to store tasks, user information, and device synchronization.
Usability

#Core Features Task management includes adding, editing, and removing tasks.
Organize your projects by classifying them using tags (personal, work, etc.).
Reminders and Due Dates: Establish due dates and get push alerts.
Status of Completion: View completed tasks and mark them as finished.
Additional Features
Cloud Syncing: Use Firebase to synchronize tasks across devices.
Task Search: Look up tasks by category or keyword.
Dark Mode: Users have the option to go into dark mode.

Design (Wireframes)

User Interface
The app's design is straightforward and understated. Among the key screens are:

Dashboard: Shows tasks arranged by due date and importance.
Add new tasks to the task creation screen by providing their title, category, priority, and due date.
Task Detail Screen: Modify or mark jobs as finished.
Experience of the User
Smooth navigation and task management are ensured by the app's bottom navigation bar, which provides quick access to the dashboard, task creation, and settings.

Testing

Unit testing for quality assurance involves examining distinct functionalities such task creation and deletion.
Integration testing: Verify that Firebase and the app's front end are communicating correctly.
User Acceptance Testing: Gather user input to make the program better.
For methodical bug fixes, GitHub Issues will be used to monitor bugs and issues.

Timeline

Milestones
Week 1 of Phase 1: Wireframe creation and planning.
Phase 2: App development 
(Weeks 2–5).
Phase 3: Testing and bug fixes (Week 6).
Phase 4: Last-minute modifications and implementation (Week 7).
Due date
The finished application ought to be available by the conclusion of the term.

Budget

Assignment of Resources
Backend services are provided by Firebase (Free Tier).
Minimal development tools, such as Figma for wireframes.
Employees: Additional design help may be hired for a $500 estimate.
Control of Risk

Risks Identified
Development that is delayed: Unexpected obstacles may cause development to take longer than anticipated.
Technical challenges include problems with cross-platform compatibility for React Native or Firebase.
Techniques for Mitigation
regular evaluations of development.
Testing integration with Firebase early.
Give bug fixes additional time.

In conclusion,

Summary  
The goal of the To-Do List App is to provide a straightforward yet efficient solution for stress management, productivity enhancement, and daily task management. It provides all the necessary tools for task organization in a clear, user-friendly interface.



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
