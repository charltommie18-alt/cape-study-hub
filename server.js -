const express = require('express');
const cors = require('cors');
const { Pool } = require('pg');
const app = express();
app.use(cors({origin: '*'}));
app.use(express.json({limit: '10mb'}));
const pool = new Pool({connectionString: process.env.DATABASE_URL, ssl: {rejectUnauthorized: false}});
app.get('/', (req,res)=> res.json({status:'Cape Hub API Live', paypal:'URJZ4DJH4RKHQ'}));
app.post('/api/users', async (req,res)=>{
  try{
    const {email,country,plan,status} = req.body;
    const r = await pool.query('INSERT INTO users(email,country,plan,status) VALUES($1,$2,$3,$4) ON CONFLICT(email) DO UPDATE SET status=$4 RETURNING *', [email,country,plan,status||'trial']);
    res.json(r.rows[0]);
  }catch(e){ res.status(500).json({error:e.message}); }
});
app.get('/api/users', async (req,res)=>{
  const r = await pool.query('SELECT * FROM users ORDER BY created_at DESC');
  res.json(r.rows);
});
app.post('/api/subscriptions', async (req,res)=>{
  const {user_email,amount,currency} = req.body;
  const r = await pool.query('INSERT INTO subscriptions(user_email,paypal_id,amount,currency,status) VALUES($1,$2,$3,$4,$5) RETURNING *', [user_email,'URJZ4DJH4RKHQ',amount,currency,'active']);
  await pool.query("UPDATE users SET status='active' WHERE email=$1", [user_email]);
  res.json(r.rows[0]);
});
app.listen(process.env.PORT||10000, ()=>console.log('Live'));
