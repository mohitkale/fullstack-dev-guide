# 📱 Mobile Development Guide

Welcome to the mobile development section! Here, you'll learn how to build the WeAll social media mobile application using React Native and Expo.

## 📋 Table of Contents
1. [Getting Started](#-getting-started)
2. [Project Setup](#-project-setup)
3. [Folder Structure](#-folder-structure)
4. [Core Components](#-core-components)
5. [Navigation](#-navigation)
6. [State Management](#-state-management)
7. [API Integration](#-api-integration)
8. [Styling](#-styling)
9. [Testing](#-testing)
10. [Building for Production](#-building-for-production)
11. [Next Steps](#-next-steps)

## 🚀 Getting Started

### Prerequisites

1. **Node.js** (v16 or newer)
2. **npm** or **yarn**
3. **Expo CLI** (for development)
4. **Android Studio** (for Android development)
5. **Xcode** (for iOS development, macOS only)

### Install Expo CLI

```bash
npm install -g expo-cli
```

## 🛠 Project Setup

1. **Clone the repository** (if you haven't already)
   ```bash
   git clone https://github.com/yourusername/mentorhub.git
   cd mentorhub/mobile
   ```

2. **Install dependencies**
   ```bash
   npm install
   # or
   yarn
   ```

3. **Start the development server**
   ```bash
   npx expo start
   ```

4. **Run on your device/emulator**
   - Scan the QR code with the Expo Go app (iOS)
   - Or press 'a' for Android emulator / 'i' for iOS simulator

## 📁 Folder Structure

```
mobile/
├── assets/            # Images, fonts, and other static files
├── components/         # Reusable components
│   ├── common/        # Common UI components
│   ├── posts/         # Post-related components
│   └── profile/       # Profile-related components
├── constants/         # App constants
├── context/           # React Context providers
├── hooks/             # Custom React hooks
├── navigation/        # Navigation configuration
├── screens/           # App screens
├── services/          # API services
├── theme/             # Styling and theming
├── types/             # TypeScript type definitions
├── utils/             # Utility functions
├── App.tsx            # Main application component
└── app.json           # Expo configuration
```

## 🧩 Core Components

### Creating a Custom Button

```tsx
// components/common/Button.tsx
import React from 'react';
import { TouchableOpacity, Text, StyleSheet, ActivityIndicator } from 'react-native';

type ButtonProps = {
  title: string;
  onPress: () => void;
  variant?: 'primary' | 'secondary' | 'outline';
  loading?: boolean;
  disabled?: boolean;
  style?: object;
};

export const Button = ({
  title,
  onPress,
  variant = 'primary',
  loading = false,
  disabled = false,
  style,
}: ButtonProps) => {
  const getButtonStyle = () => {
    switch (variant) {
      case 'secondary':
        return styles.secondaryButton;
      case 'outline':
        return styles.outlineButton;
      case 'primary':
      default:
        return styles.primaryButton;
    }
  };

  const getTextStyle = () => {
    switch (variant) {
      case 'outline':
        return styles.outlineText;
      default:
        return styles.buttonText;
    }
  };

  return (
    <TouchableOpacity
      style={[styles.button, getButtonStyle(), style, (disabled || loading) && styles.disabledButton]}
      onPress={onPress}
      disabled={disabled || loading}
      activeOpacity={0.7}
    >
      {loading ? (
        <ActivityIndicator color="#fff" />
      ) : (
        <Text style={[getTextStyle(), disabled && styles.disabledText]}>{title}</Text>
      )}
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  button: {
    padding: 15,
    borderRadius: 8,
    alignItems: 'center',
    justifyContent: 'center',
    marginVertical: 8,
    flexDirection: 'row',
  },
  primaryButton: {
    backgroundColor: '#007AFF',
  },
  secondaryButton: {
    backgroundColor: '#5856D6',
  },
  outlineButton: {
    backgroundColor: 'transparent',
    borderWidth: 1,
    borderColor: '#007AFF',
  },
  disabledButton: {
    opacity: 0.6,
  },
  buttonText: {
    color: '#fff',
    fontSize: 16,
    fontWeight: '600',
  },
  outlineText: {
    color: '#007AFF',
    fontSize: 16,
    fontWeight: '600',
  },
  disabledText: {
    color: '#A0A0A0',
  },
});
```

## 🧭 Navigation

We use React Navigation for handling navigation in our app.

### Setting Up Navigation

```tsx
// navigation/AppNavigator.tsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

// Screens
import { HomeScreen } from '../screens/HomeScreen';
import { ProfileScreen } from '../screens/ProfileScreen';
import { CreatePostScreen } from '../screens/CreatePostScreen';
import { NotificationsScreen } from '../screens/NotificationsScreen';
import { LoginScreen } from '../screens/LoginScreen';
import { RegisterScreen } from '../screens/RegisterScreen';

// Icons
import { Ionicons } from '@expo/vector-icons';

const Stack = createNativeStackNavigator();
const Tab = createBottomTabNavigator();

function HomeTabs() {
  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          let iconName;

          if (route.name === 'Home') {
            iconName = focused ? 'home' : 'home-outline';
          } else if (route.name === 'Profile') {
            iconName = focused ? 'person' : 'person-outline';
          } else if (route.name === 'Create') {
            iconName = 'add-circle';
            return <Ionicons name={iconName} size={size + 10} color={color} />;
          } else if (route.name === 'Notifications') {
            iconName = focused ? 'notifications' : 'notifications-outline';
          }

          return <Ionicons name={iconName} size={size} color={color} />;
        },
        tabBarActiveTintColor: '#007AFF',
        tabBarInactiveTintColor: 'gray',
        headerShown: false,
      })}
    >
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen 
        name="Create" 
        component={CreatePostScreen} 
        options={{
          tabBarLabel: '',
          tabBarIconStyle: {
            marginTop: 8,
          },
        }}
      />
      <Tab.Screen name="Notifications" component={NotificationsScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  );
}

export function AppNavigator() {
  const isAuthenticated = false; // Replace with your auth state

  return (
    <NavigationContainer>
      <Stack.Navigator screenOptions={{ headerShown: false }}>
        {isAuthenticated ? (
          <Stack.Screen name="App" component={HomeTabs} />
        ) : (
          <>
            <Stack.Screen name="Login" component={LoginScreen} />
            <Stack.Screen name="Register" component={RegisterScreen} />
          </>
        )}
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

## 🧠 State Management

We'll use React Context for global state management. Here's an example of an authentication context:

```tsx
// context/AuthContext.tsx
import React, { createContext, useState, useContext, useEffect } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { authService } from '../services/authService';

type User = {
  id: string;
  name: string;
  email: string;
  avatar?: string;
};

type AuthContextData = {
  user: User | null;
  loading: boolean;
  signIn: (email: string, password: string) => Promise<void>;
  signUp: (name: string, email: string, password: string) => Promise<void>;
  signOut: () => Promise<void>;
  updateUser: (user: User) => Promise<void>;
};

const AuthContext = createContext<AuthContextData>({} as AuthContextData);

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    loadStoredData();
  }, []);

  async function loadStoredData() {
    try {
      const storedUser = await AsyncStorage.getItem('@WeAll:user');
      
      if (storedUser) {
        setUser(JSON.parse(storedUser));
      }
    } catch (error) {
      console.error('Failed to load stored data', error);
    } finally {
      setLoading(false);
    }
  }

  async function signIn(email: string, password: string) {
    try {
      const response = await authService.signIn(email, password);
      
      setUser(response.user);
      await AsyncStorage.setItem('@WeAll:user', JSON.stringify(response.user));
      await AsyncStorage.setItem('@WeAll:token', response.token);
    } catch (error) {
      throw new Error('Failed to sign in');
    }
  }

  async function signUp(name: string, email: string, password: string) {
    try {
      const response = await authService.signUp(name, email, password);
      
      setUser(response.user);
      await AsyncStorage.setItem('@WeAll:user', JSON.stringify(response.user));
      await AsyncStorage.setItem('@WeAll:token', response.token);
    } catch (error) {
      throw new Error('Failed to create account');
    }
  }

  async function signOut() {
    try {
      await authService.signOut();
      setUser(null);
      await AsyncStorage.removeItem('@WeAll:user');
      await AsyncStorage.removeItem('@WeAll:token');
    } catch (error) {
      throw new Error('Failed to sign out');
    }
  }

  async function updateUser(updatedUser: User) {
    try {
      setUser(updatedUser);
      await AsyncStorage.setItem('@WeAll:user', JSON.stringify(updatedUser));
    } catch (error) {
      throw new Error('Failed to update user');
    }
  }

  return (
    <AuthContext.Provider
      value={{
        user,
        loading,
        signIn,
        signUp,
        signOut,
        updateUser,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
};

export function useAuth() {
  const context = useContext(AuthContext);
  
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  
  return context;
}
```

## 🌐 API Integration

### API Service Example

```typescript
// services/api.ts
import axios from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

const api = axios.create({
  baseURL: 'https://api.weall.app', // Replace with your API URL
  timeout: 10000,
});

// Request interceptor to add auth token
api.interceptors.request.use(
  async (config) => {
    const token = await AsyncStorage.getItem('@WeAll:token');
    
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Response interceptor
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    // Handle 401 Unauthorized errors (token expired, etc.)
    if (error.response?.status === 401) {
      // Clear auth data and redirect to login
      await AsyncStorage.removeItem('@WeAll:user');
      await AsyncStorage.removeItem('@WeAll:token');
      // You might want to use a navigation ref to navigate to login
    }
    
    return Promise.reject(error);
  }
);

export default api;

// Auth service example
export const authService = {
  async signIn(email: string, password: string) {
    const response = await api.post('/auth/signin', { email, password });
    return response.data;
  },
  
  async signUp(name: string, email: string, password: string) {
    const response = await api.post('/auth/signup', { name, email, password });
    return response.data;
  },
  
  async signOut() {
    await api.post('/auth/signout');
  },
};
```

## 🎨 Styling

We use StyleSheet for styling our components. Here's an example of a theme system:

```typescript
// theme/colors.ts
export const colors = {
  primary: '#007AFF',
  secondary: '#5856D6',
  background: '#FFFFFF',
  card: '#F8F8FA',
  text: '#1C1C1E',
  border: '#E5E5EA',
  notification: '#FF3B30',
  success: '#34C759',
  warning: '#FF9500',
  gray: {
    100: '#F2F2F7',
    200: '#E5E5EA',
    300: '#D1D1D6',
    400: '#C7C7CC',
    500: '#AEAEB2',
    600: '#8E8E93',
    700: '#636366',
    800: '#48484A',
    900: '#3A3A3C',
  },
};

// theme/typography.ts
export const typography = {
  h1: {
    fontSize: 32,
    fontWeight: 'bold',
    lineHeight: 38,
  },
  h2: {
    fontSize: 28,
    fontWeight: '600',
    lineHeight: 32,
  },
  h3: {
    fontSize: 22,
    fontWeight: '600',
    lineHeight: 28,
  },
  body: {
    fontSize: 16,
    lineHeight: 24,
  },
  caption: {
    fontSize: 14,
    lineHeight: 20,
    color: colors.gray[600],
  },
};

// theme/spacing.ts
export const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48,
};

export const borderRadius = {
  sm: 4,
  md: 8,
  lg: 16,
  full: 9999,
};
```

## 🧪 Testing

We use Jest and React Native Testing Library for testing our components.

### Example Test

```typescript
// __tests__/components/Button.test.tsx
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import { Button } from '../../components/common/Button';

describe('Button', () => {
  it('renders correctly with default props', () => {
    const { getByText } = render(<Button title="Press me" onPress={() => {}} />);
    expect(getByText('Press me')).toBeTruthy();
  });

  it('calls onPress when pressed', () => {
    const onPressMock = jest.fn();
    const { getByText } = render(<Button title="Press me" onPress={onPressMock} />);
    
    fireEvent.press(getByText('Press me'));
    expect(onPressMock).toHaveBeenCalled();
  });

  it('shows loading indicator when loading is true', () => {
    const { getByTestId } = render(
      <Button title="Press me" onPress={() => {}} loading />
    );
    
    expect(getByTestId('activity-indicator')).toBeTruthy();
  });
});
```

## 🚀 Building for Production

### 1. Create a production build

```bash
# For Android
npx expo build:android

# For iOS (requires macOS)
npx expo build:ios
```

### 2. Publish to app stores

#### Android (Google Play Store)
1. Generate a signed APK or Android App Bundle (AAB)
2. Create a Google Play Developer account
3. Upload your app to the Google Play Console

#### iOS (App Store)
1. Create an Apple Developer account
2. Set up your app in App Store Connect
3. Upload your app using Transporter or Xcode

## 📚 Next Steps

1. [Learn about backend integration](../backend/README.md)
2. [Explore web development](../frontend/README.md)
3. [Check out the component library](./COMPONENTS.md)
4. [Read about performance optimization](./PERFORMANCE.md)

## 🔗 Additional Resources

- [React Native Documentation](https://reactnative.dev/)
- [Expo Documentation](https://docs.expo.dev/)
- [React Navigation](https://reactnavigation.org/)
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)

Happy coding! 🎉
