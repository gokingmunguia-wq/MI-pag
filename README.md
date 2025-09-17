# MI-pag
import { useState } from "react";
import { BrowserRouter as Router, Routes, Route, Link, Navigate } from "react-router-dom";

function Navbar({ isAdmin, setIsAdmin }) {
  return (
    <nav className="bg-blue-900 text-white p-4 flex justify-between">
      <div className="font-bold">SACMEX</div>
      <div className="space-x-4">
        <Link to="/">Inicio</Link>
        <Link to="/presentaciones">Presentaciones</Link>
        {isAdmin && <Link to="/editor">Editor</Link>}
        {!isAdmin ? (
          <Link to="/login">Login</Link>
        ) : (
          <button onClick={() => setIsAdmin(false)}>Salir</button>
        )}
      </div>
    </nav>
  );
}

function Home() {
  return (
    <div className="p-8">
      <h1 className="text-3xl font-bold text-blue-900">Bienvenido a SACMEX</h1>
      <p className="mt-4 text-gray-700">
        Plataforma institucional para gestión y visualización de presentaciones.
      </p>
    </div>
  );
}

function Presentaciones({ presentations }) {
  return (
    <div className="p-8">
      <h2 className="text-2xl font-bold mb-4">Presentaciones disponibles</h2>
      {presentations.length === 0 ? (
        <p>No hay presentaciones todavía.</p>
      ) : (
        presentations.map((p, i) => (
          <div key={i} className="border p-4 mb-2 rounded bg-gray-50">
            <h3 className="font-semibold">{p.title}</h3>
            <ul className="list-disc ml-5">
              {p.slides.map((s, j) => (
                <li key={j}>{s}</li>
              ))}
            </ul>
          </div>
        ))
      )}
    </div>
  );
}

function Editor({ presentations, setPresentations }) {
  const [title, setTitle] = useState("");
  const [slides, setSlides] = useState([""]);

  const savePresentation = () => {
    setPresentations([...presentations, { title, slides }]);
    setTitle("");
    setSlides([""]);
  };

  return (
    <div className="p-8">
      <h2 className="text-2xl font-bold mb-4">Editor de presentaciones</h2>
      <input
        type="text"
        placeholder="Título"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        className="border p-2 w-full mb-4"
      />
      {slides.map((s, i) => (
        <input
          key={i}
          type="text"
          placeholder={`Diapositiva ${i + 1}`}
          value={s}
          onChange={(e) => {
            const newSlides = [...slides];
            newSlides[i] = e.target.value;
            setSlides(newSlides);
          }}
          className="border p-2 w-full mb-2"
        />
      ))}
      <button
        onClick={() => setSlides([...slides, ""])}
        className="bg-blue-700 text-white px-4 py-2 rounded mr-2"
      >
        + Agregar diapositiva
      </button>
      <button
        onClick={savePresentation}
        className="bg-green-600 text-white px-4 py-2 rounded"
      >
        Guardar presentación
      </button>
    </div>
  );
}

function Login({ setIsAdmin }) {
  const [password, setPassword] = useState("");

  const handleLogin = () => {
    if (password === "sacmex123") {
      setIsAdmin(true);
    } else {
      alert("Contraseña incorrecta");
    }
  };

  return (
    <div className="p-8">
      <h2 className="text-2xl font-bold mb-4">Login de administrador</h2>
      <input
        type="password"
        placeholder="Contraseña"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        className="border p-2 w-full mb-4"
      />
      <button
        onClick={handleLogin}
        className="bg-blue-700 text-white px-4 py-2 rounded"
      >
        Entrar
      </button>
    </div>
  );
}

export default function App() {
  const [isAdmin, setIsAdmin] = useState(false);
  const [presentations, setPresentations] = useState([]);

  return (
    <Router>
      <Navbar isAdmin={isAdmin} setIsAdmin={setIsAdmin} />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route
          path="/presentaciones"
          element={<Presentaciones presentations={presentations} />}
        />
        <Route
          path="/editor"
          element={
            isAdmin ? (
              <Editor
                presentations={presentations}
                setPresentations={setPresentations}
              />
            ) : (
              <Navigate to="/login" />
            )
          }
        />
        <Route path="/login" element={<Login setIsAdmin={setIsAdmin} />} />
      </Routes>
    </Router>
  );
}
