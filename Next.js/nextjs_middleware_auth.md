# Next.js Middleware per Autenticazione JWT

Un middleware completo per Next.js che verifica la presenza e validità di un JWT nei cookie e reindirizza gli utenti non autenticati alla pagina di login.

## Implementazione del Middleware

Crea il file `middleware.ts` nella root del progetto:

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { jwtVerify } from 'jose'

// Percorsi che richiedono autenticazione
const protectedRoutes = ['/dashboard', '/profile', '/admin', '/api/protected']

// Percorsi pubblici (non richiedono autenticazione) 
const publicRoutes = ['/login', '/register', '/forgot-password', '/', '/about']

export async function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl
  
  // Controlla se il percorso richiede autenticazione
  const isProtectedRoute = protectedRoutes.some(route => 
    pathname.startsWith(route)
  )
  
  const isPublicRoute = publicRoutes.includes(pathname)
  
  // Se il percorso non è protetto, continua
  if (!isProtectedRoute) {
    return NextResponse.next()
  }
  
  // Recupera il token JWT dai cookie
  const token = request.cookies.get('auth-token')?.value
  
  if (!token) {
    return redirectToLogin(request)
  }
  
  try {
    // Verifica il JWT usando la libreria jose
    const secret = new TextEncoder().encode(
      process.env.JWT_SECRET || 'your-fallback-secret-key'
    )
    
    const { payload } = await jwtVerify(token, secret)
    
    // Controlla se il token è scaduto (doppio controllo)
    if (payload.exp && payload.exp < Date.now() / 1000) {
      return redirectToLogin(request)
    }
    
    // Aggiunge le informazioni dell'utente agli headers per le API routes
    const response = NextResponse.next()
    response.headers.set('x-user-id', payload.userId as string)
    response.headers.set('x-user-role', payload.role as string || 'user')
    response.headers.set('x-user-email', payload.email as string)
    
    return response
    
  } catch (error) {
    console.error('JWT verification failed:', error)
    return redirectToLogin(request)
  }
}

function redirectToLogin(request: NextRequest) {
  const loginUrl = new URL('/login', request.url)
  
  // Salva l'URL di destinazione originale per il redirect post-login
  loginUrl.searchParams.set('callbackUrl', request.nextUrl.pathname)
  
  const response = NextResponse.redirect(loginUrl)
  
  // Rimuove il cookie del token non valido
  response.cookies.delete('auth-token')
  
  return response
}

// Configura su quali percorsi il middleware deve essere eseguito
export const config = {
  matcher: [
    /*
     * Esegui il middleware su tutti i percorsi eccetto:
     * - api routes di autenticazione (/api/auth/*)
     * - file statici (_next/static)
     * - ottimizzazione immagini (_next/image)
     * - favicon.ico
     * - file con estensioni (.png, .jpg, .svg, etc.)
     */
    '/((?!api/auth|_next/static|_next/image|favicon.ico|.*\\..*).*)',
  ],
}
```

## Installazione dipendenze

```bash
npm install jose
# oppure
yarn add jose
# oppure
pnpm add jose
```

## API Route per il Login

Crea il file `app/api/auth/login/route.ts`:

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { SignJWT } from 'jose'
import bcrypt from 'bcryptjs'

// Simula un database di utenti (sostituisci con il tuo DB)
const users = [
  {
    id: '1',
    email: 'user@example.com',
    password: '$2a$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/LewgC7GSdqL4M.3Ca', // "password123"
    role: 'user'
  }
]

export async function POST(request: NextRequest) {
  try {
    const { email, password } = await request.json()
    
    // Trova l'utente nel database
    const user = users.find(u => u.email === email)
    
    if (!user || !await bcrypt.compare(password, user.password)) {
      return NextResponse.json(
        { error: 'Credenziali non valide' },
        { status: 401 }
      )
    }
    
    // Crea il JWT
    const secret = new TextEncoder().encode(process.env.JWT_SECRET!)
    const token = await new SignJWT({
      userId: user.id,
      email: user.email,
      role: user.role,
    })
      .setProtectedHeader({ alg: 'HS256' })
      .setExpirationTime('7d') // Token valido per 7 giorni
      .setIssuedAt()
      .sign(secret)
    
    // Crea la risposta e imposta il cookie
    const response = NextResponse.json(
      { 
        message: 'Login effettuato con successo',
        user: { id: user.id, email: user.email, role: user.role }
      },
      { status: 200 }
    )
    
    // Imposta il cookie con il token
    response.cookies.set('auth-token', token, {
      httpOnly: true, // Previene accesso da JavaScript client-side
      secure: process.env.NODE_ENV === 'production', // Solo HTTPS in produzione
      sameSite: 'lax', // Protezione CSRF
      maxAge: 60 * 60 * 24 * 7, // 7 giorni in secondi
      path: '/', // Cookie disponibile su tutto il sito
    })
    
    return response
    
  } catch (error) {
    console.error('Errore durante il login:', error)
    return NextResponse.json(
      { error: 'Errore interno del server' },
      { status: 500 }
    )
  }
}
```

## API Route per il Logout

Crea il file `app/api/auth/logout/route.ts`:

```typescript
import { NextResponse } from 'next/server'

export async function POST() {
  const response = NextResponse.json(
    { message: 'Logout effettuato con successo' },
    { status: 200 }
  )
  
  // Rimuove il cookie di autenticazione
  response.cookies.delete('auth-token')
  
  return response
}
```

## Componente Login

Crea il file `app/login/page.tsx`:

```tsx
'use client'

import { useState } from 'react'
import { useRouter, useSearchParams } from 'next/navigation'

export default function LoginPage() {
  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState('')
  
  const router = useRouter()
  const searchParams = useSearchParams()
  
  // URL di redirect dopo il login
  const callbackUrl = searchParams.get('callbackUrl') || '/dashboard'
  
  const handleLogin = async (e: React.FormEvent) => {
    e.preventDefault()
    setLoading(true)
    setError('')
    
    try {
      const response = await fetch('/api/auth/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email, password }),
      })
      
      const data = await response.json()
      
      if (response.ok) {
        // Redirect all'URL originale o alla dashboard
        router.push(callbackUrl)
        router.refresh() // Aggiorna per riflettere il nuovo stato di autenticazione
      } else {
        setError(data.error || 'Errore durante il login')
      }
    } catch (error) {
      console.error('Errore durante il login:', error)
      setError('Errore di connessione')
    } finally {
      setLoading(false)
    }
  }
  
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <div className="max-w-md w-full space-y-8">
        <div>
          <h2 className="mt-6 text-center text-3xl font-extrabold text-gray-900">
            Accedi al tuo account
          </h2>
        </div>
        
        <form className="mt-8 space-y-6" onSubmit={handleLogin}>
          {error && (
            <div className="rounded-md bg-red-50 p-4">
              <div className="text-sm text-red-700">{error}</div>
            </div>
          )}
          
          <div className="rounded-md shadow-sm -space-y-px">
            <div>
              <input
                id="email"
                name="email"
                type="email"
                autoComplete="email"
                required
                className="relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
                placeholder="Indirizzo email"
                value={email}
                onChange={(e) => setEmail(e.target.value)}
              />
            </div>
            <div>
              <input
                id="password"
                name="password"
                type="password"
                autoComplete="current-password"
                required
                className="relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
                placeholder="Password"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
              />
            </div>
          </div>
          
          <div>
            <button
              type="submit"
              disabled={loading}
              className="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 disabled:opacity-50 disabled:cursor-not-allowed"
            >
              {loading ? 'Accesso in corso...' : 'Accedi'}
            </button>
          </div>
        </form>
      </div>
    </div>
  )
}
```

## Hook per verificare l'autenticazione (Client-side)

Crea il file `hooks/useAuth.ts`:

```typescript
'use client'

import { useState, useEffect } from 'react'

interface User {
  id: string
  email: string
  role: string
}

export function useAuth() {
  const [user, setUser] = useState<User | null>(null)
  const [loading, setLoading] = useState(true)
  
  useEffect(() => {
    checkAuth()
  }, [])
  
  const checkAuth = async () => {
    try {
      const response = await fetch('/api/auth/me')
      if (response.ok) {
        const userData = await response.json()
        setUser(userData)
      } else {
        setUser(null)
      }
    } catch (error) {
      console.error('Errore verifica autenticazione:', error)
      setUser(null)
    } finally {
      setLoading(false)
    }
  }
  
  const logout = async () => {
    try {
      await fetch('/api/auth/logout', { method: 'POST' })
      setUser(null)
      window.location.href = '/login'
    } catch (error) {
      console.error('Errore durante il logout:', error)
    }
  }
  
  return {
    user,
    loading,
    isAuthenticated: !!user,
    logout,
    refetch: checkAuth
  }
}
```

## API Route per verificare l'utente corrente

Crea il file `app/api/auth/me/route.ts`:

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { jwtVerify } from 'jose'

export async function GET(request: NextRequest) {
  try {
    const token = request.cookies.get('auth-token')?.value
    
    if (!token) {
      return NextResponse.json(
        { error: 'Token non presente' },
        { status: 401 }
      )
    }
    
    const secret = new TextEncoder().encode(process.env.JWT_SECRET!)
    const { payload } = await jwtVerify(token, secret)
    
    return NextResponse.json({
      id: payload.userId,
      email: payload.email,
      role: payload.role
    })
    
  } catch (error) {
    console.error('Errore verifica token:', error)
    return NextResponse.json(
      { error: 'Token non valido' },
      { status: 401 }
    )
  }
}
```

## Variabili d'ambiente

Crea o aggiorna il file `.env.local`:

```bash
# Chiave segreta per firmare i JWT (usa una chiave sicura e casuale in produzione)
JWT_SECRET=your-super-secret-jwt-key-minimum-32-characters-long

# URL del database (se necessario)
DATABASE_URL=your-database-connection-string

# Ambiente
NODE_ENV=development
```

## Esempio di utilizzo in una pagina protetta

```tsx
// app/dashboard/page.tsx
import { cookies } from 'next/headers'
import { jwtVerify } from 'jose'
import { redirect } from 'next/navigation'

async function verifyAuth() {
  const cookieStore = await cookies()
  const token = cookieStore.get('auth-token')?.value
  
  if (!token) {
    redirect('/login')
  }
  
  try {
    const secret = new TextEncoder().encode(process.env.JWT_SECRET!)
    const { payload } = await jwtVerify(token, secret)
    return payload
  } catch (error) {
    redirect('/login')
  }
}

export default async function DashboardPage() {
  const user = await verifyAuth()
  
  return (
    <div className="p-8">
      <h1 className="text-2xl font-bold mb-4">
        Benvenuto, {user.email}!
      </h1>
      <p>Questo è il tuo dashboard privato.</p>
      <p>Ruolo: {user.role}</p>
    </div>
  )
}
```

## Caratteristiche principali

1. **Verifica JWT automatica**: Il middleware controlla automaticamente tutti i percorsi protetti
2. **Redirect intelligente**: Salva l'URL originale per reindirizzare dopo il login
3. **Headers utente**: Aggiunge informazioni dell'utente agli headers per le API routes
4. **Gestione cookie sicura**: Cookie HTTP-only, secure in produzione, con SameSite
5. **Pulizia automatica**: Rimuove cookie non validi automaticamente
6. **Performance ottimizzata**: Matcher configurato per escludere file statici

## Note di sicurezza

- Usa sempre una `JWT_SECRET` lunga e casuale in produzione
- Abilita `secure: true` per i cookie in produzione (HTTPS)
- Considera l'implementazione di refresh token per sessioni lunghe
- Implementa rate limiting per le API di autenticazione
- Valida sempre i dati di input nelle API routes