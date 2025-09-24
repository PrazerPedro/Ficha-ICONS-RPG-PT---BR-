      border-radius: 6px;
      cursor: pointer;
    }
    .btn:hover {
      opacity: 0.9;
    }
  </style>
</head>
<body class="bg-gray-100">
  <div id="root"></div>

  <script type="text/babel">
    function FichaIcons() {
      const [name, setName] = React.useState('');
      const [alias, setAlias] = React.useState('');
      const [origin, setOrigin] = React.useState('');
      const [line, setLine] = React.useState('Linha Principal');
      const [description, setDescription] = React.useState('');
      const [notes, setNotes] = React.useState('');
      const baseAttrs = { Proeza: 3, Coordenacao: 3, Vigor: 3, Intelecto: 3, Atencao: 3, Vontade: 3 };
      const [attrs, setAttrs] = React.useState(baseAttrs);
      const [powers, setPowers] = React.useState([]);
      const [specializations, setSpecializations] = React.useState([]);
      const [conditions, setConditions] = React.useState(['', '', '']);
      const [powerDraft, setPowerDraft] = React.useState({ name: '', level: 3, extras: '', limits: '' });
      const [specDraft, setSpecDraft] = React.useState('');
      const energy = Number(attrs.Vigor || 0) + Number(attrs.Vontade || 0);
      const determination = Math.max(1, 6 - powers.length - Object.values(attrs).filter(v => v > 6).length);

      // Carrega do localStorage
      React.useEffect(() => {
        const saved = localStorage.getItem('ficha_icons');
        if (saved) {
          try {
            const obj = JSON.parse(saved);
            setName(obj.name || '');
            setAlias(obj.alias || '');
            setOrigin(obj.origin || '');
            setLine(obj.line || 'Linha Principal');
            setDescription(obj.description || '');
            setNotes(obj.notes || '');
            setAttrs(obj.attrs || baseAttrs);
            setPowers(obj.powers || []);
            setSpecializations(obj.specializations || []);
            setConditions(obj.conditions || ['', '', '']);
          } catch {}
        }
      }, []);

      // Salva no localStorage
      React.useEffect(() => {
        const obj = { name, alias, origin, line, description, notes, attrs, powers, specializations, conditions };
        localStorage.setItem('ficha_icons', JSON.stringify(obj));
      }, [name, alias, origin, line, description, notes, attrs, powers, specializations, conditions]);

      function changeAttr(key, value) {
        const n = parseInt(value || 0, 10);
        if (Number.isNaN(n)) return;
        setAttrs(prev => ({ ...prev, [key]: Math.max(1, Math.min(10, n)) }));
      }

      function addPower() {
        if (!powerDraft.name) return;
        setPowers(prev => [...prev, { ...powerDraft }]);
        setPowerDraft({ name: '', level: 3, extras: '', limits: '' });
      }
      function removePower(i) { setPowers(prev => prev.filter((_, idx) => idx !== i)); }
      function addSpec() { if (!specDraft) return; setSpecializations(prev => [...prev, specDraft]); setSpecDraft(''); }
      function removeSpec(i) { setSpecializations(prev => prev.filter((_, idx) => idx !== i)); }
      function updateCondition(i, val) { const next = [...conditions]; next[i] = val; setConditions(next); }

      return (
        <div className="p-6 max-w-6xl mx-auto font-sans">
          <h1 className="text-3xl font-bold mb-4">Ficha Icons RPG</h1>
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            {/* Identidade */}
            <section className="bg-white p-4 rounded shadow">
              <h2 className="text-xl font-semibold mb-3">Identidade</h2>
              <input className="input" placeholder="Nome" value={name} onChange={e=>setName(e.target.value)} />
              <input className="input mt-2" placeholder="Codinome" value={alias} onChange={e=>setAlias(e.target.value)} />
              <select className="input mt-2" value={origin} onChange={e=>setOrigin(e.target.value)}>
                <option value="">Origem</option>
                <option>Treinado</option><option>Transformado</option><option>Nascido</option><option>Extraterrestre</option><option>Artificial</option><option>Engenhocas</option>
              </select>
              <select className="input mt-2" value={line} onChange={e=>setLine(e.target.value)}>
                <option>Linha Principal</option><option>Linha Média</option><option>Linha de Apoio</option>
              </select>
              <p className="mt-4">Energia: <strong>{energy}</strong></p>
              <p>Determinação: <strong>{determination}</strong></p>
              <h3 className="font-medium mt-4">Condições</h3>
              {conditions.map((c, i) => (
                <input key={i} className="input mt-1" placeholder={`Condição ${i+1}`} value={c} onChange={e=>updateCondition(i,e.target.value)} />
              ))}
            </section>

            {/* Atributos */}
            <section className="bg-white p-4 rounded shadow">
              <h2 className="text-xl font-semibold mb-3">Atributos</h2>
              {Object.keys(attrs).map(key => (
                <div key={key} className="mt-2">
                  <label className="block text-sm">{key}</label>
                  <input type="number" className="input" value={attrs[key]} onChange={e=>changeAttr(key,e.target.value)} />
                </div>
              ))}
              <h3 className="font-medium mt-4">Especializações</h3>
              <div className="flex gap-2 mt-2">
                <input value={specDraft} onChange={e=>setSpecDraft(e.target.value)} className="input" placeholder="Nova especialização" />
                <button onClick={addSpec} className="btn">Adicionar</button>
              </div>
              <ul className="mt-2">
                {specializations.map((s,i)=>(
                  <li key={i}>{s} <button onClick={()=>removeSpec(i)} className="text-red-500">x</button></li>
                ))}
              </ul>
            </section>

            {/* Poderes */}
            <section className="bg-white p-4 rounded shadow">
              <h2 className="text-xl font-semibold mb-3">Poderes</h2>
              <input className="input" placeholder="Nome" value={powerDraft.name} onChange={e=>setPowerDraft({...powerDraft,name:e.target.value})} />
              <input className="input mt-2" type="number" value={powerDraft.level} onChange={e=>setPowerDraft({...powerDraft,level:parseInt(e.target.value||0,10)})} />
              <input className="input mt-2" placeholder="Extras" value={powerDraft.extras} onChange={e=>setPowerDraft({...powerDraft,extras:e.target.value})} />
              <input className="input mt-2" placeholder="Limites" value={powerDraft.limits} onChange={e=>setPowerDraft({...powerDraft,limits:e.target.value})} />
              <button onClick={addPower} className="btn mt-2">Adicionar</button>
              <ul className="mt-2">
                {powers.map((p,i)=>(
                  <li key={i}>
                    <strong>{p.name}</strong> (Nível {p.level}) {p.extras} {p.limits}
                    <button onClick={()=>removePower(i)} className="text-red-500 ml-2">x</button>
                  </li>
                ))}
              </ul>
              <textarea className="input mt-4 h-24" placeholder="Descrição/histórico" value={description} onChange={e=>setDescription(e.target.value)} />
              <textarea className="input mt-2 h-24" placeholder="Notas" value={notes} onChange={e=>setNotes(e.target.value)} />
            </section>
          </div>
        </div>
      );
    }

    ReactDOM.createRoot(document.getElementById('root')).render(<FichaIcons/>);
  </script>
</body>
</html>
