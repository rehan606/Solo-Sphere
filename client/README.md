### Delete Data server side

```js
    app.delete('/job/:id', async(req, res) => {
      const id = req.params.id
      const query = {_id: new ObjectId(id)}
      const result = await jobsCollection.deleteOne(query)
      res.send(result)
    })
```

### Delete Handler client side

```js
    const handleDelete = async (id) => {
        try {
            const { data } = await axios.delete(`${import.meta.env.VITE_API_URL}/job/${id}`)
            toast.success('Data added Successfully')
            fetchAllJobs()
        } catch (err) {
            console.log(err)
            toast.error(err.message)
        }
    }
```
### Delete icon or button
```js
    <button onClick={() => mordenDelete(job._id)}> Delete </button>

```



# Update data in database and UI

```js
    app.get('/job/:id', async(req, res) => {
        const id = req.params.id
        const query = {_id: new ObjectId(id)}
        const result = await jobsCollection.findOne(query)
        res.send(result)
    })
```

Update.jsx 

```js
  const [startDate, setStartDate] = useState(new Date()) // Date Piker
  const {user} = useContext(AuthContext) //User context
  const [job, setJob] = useState({}) // job State
  const {id} = useParams() // use params

  useEffect(() => {
    fetchJobData()
  },[id])

  const fetchJobData = async() => {
    const {data} = await axios.get(`${import.meta.env.VITE_API_URL}/job/${id}`)
    setJob(data)
    setStartDate(new Date(data.deadline))
  }
```

### Update handler copy from add job component

```js
    const handleSubmit =async e => {
        e.preventDefault()
        const form = e.target
        const title = form.job_title.value;
        const email = form.email.value;
        const deadline = startDate;
        const category = form.category.value;
        const min_price = parseFloat(form.min_price.value);
        const max_price = parseFloat(form.max_price.value);
        const description = form.description.value;

        const formData = {
        title, 
        buyer:{
            email,
            name: user?.displayName,
            photo: user?.photoURL,
        },
        deadline, 
        category, 
        min_price, 
        max_price, 
        description,
        bid_count: job.bid_count,
        }

        console.log(formData);

        // Make a post request 
        // const {data} = await axios.post(`${import.meta.env.VITE_API_URL}/add-job`, formData)
        try{
            await axios.put(`${import.meta.env.VITE_API_URL}/update-job/${id}`, formData)
            form.reset()
            toast.success('Data Updated Successfully')
            navigate('/my-posted-jobs')
        } catch (err){
            console.log(err);
            toast.error(err.message)
        }
    }

```

### form

Update.jsx
```js
// Get data in form input field using defaultValue={}

    <form onSubmit={handleSubmit}>
        <div className='grid grid-cols-1 gap-6 mt-4 sm:grid-cols-2'>
            <div>
              <label className='text-gray-700 ' htmlFor='job_title'>
                Job Title
              </label>
              <input
                id='job_title'
                name='job_title'
                defaultValue={job.title}
                type='text'
                className='block w-full px-4 py-2 mt-2 text-gray-700 bg-white border border-gray-200 rounded-md  focus:border-blue-400 focus:ring-blue-300 focus:ring-opacity-40  focus:outline-none focus:ring'
              />
            </div>

            <div>
              <label className='text-gray-700 ' htmlFor='emailAddress'>
                Email Address
              </label>
              <input
                id='emailAddress'
                type='email'
                name='email'
                defaultValue={user?.email}
                disabled
                className='block w-full px-4 py-2 mt-2 text-gray-700 bg-white border border-gray-200 rounded-md  focus:border-blue-400 focus:ring-blue-300 focus:ring-opacity-40  focus:outline-none focus:ring'
              />
            </div>
            <div className='flex flex-col gap-2 '>
              <label className='text-gray-700'>Deadline</label>

              <DatePicker
                className='border p-2 rounded-md'
                selected={startDate}
                defaultValue={job.deadline}
                onChange={date => setStartDate(date)}
              />
            </div>

            {job.category && (
              <div className='flex flex-col gap-2 '>
              <label className='text-gray-700 ' htmlFor='category'>
                Category
              </label>
              <select
                name='category'
                id='category'
                defaultValue={job.category}
                className='border p-2 rounded-md'
              >
                <option value='Web Development'>Web Development</option>
                <option value='Graphics Design'>Graphics Design</option>
                <option value='Digital Marketing'>Digital Marketing</option>
              </select>
            </div>
            )}


            <div>
              <label className='text-gray-700 ' htmlFor='min_price'>
                Minimum Price
              </label>
              <input
                id='min_price'
                name='min_price'
                defaultValue={job.min_price}
                type='number'
                className='block w-full px-4 py-2 mt-2 text-gray-700 bg-white border border-gray-200 rounded-md  focus:border-blue-400 focus:ring-blue-300 focus:ring-opacity-40  focus:outline-none focus:ring'
              />
            </div>

            <div>
              <label className='text-gray-700 ' htmlFor='max_price'>
                Maximum Price
              </label>
              <input
                id='max_price'
                name='max_price'
                defaultValue={job.max_price}
                type='number'
                className='block w-full px-4 py-2 mt-2 text-gray-700 bg-white border border-gray-200 rounded-md  focus:border-blue-400 focus:ring-blue-300 focus:ring-opacity-40  focus:outline-none focus:ring'
              />
            </div>
          </div>
          <div className='flex flex-col gap-2 mt-4'>
            <label className='text-gray-700 ' htmlFor='description'>
              Description
            </label>
            <textarea
              className='block w-full px-4 py-2 mt-2 text-gray-700 bg-white border border-gray-200 rounded-md  focus:border-blue-400 focus:ring-blue-300 focus:ring-opacity-40  focus:outline-none focus:ring'
              name='description'
              id='description'
              defaultValue={job.description}
              cols='30'
            ></textarea>
          </div>
          <div className='flex justify-end mt-6'>
            <button className='px-8 py-2.5 leading-5 text-white transition-colors duration-300 transhtmlForm bg-gray-700 rounded-md hover:bg-gray-600 focus:outline-none focus:bg-gray-600'>
              Save
            </button>
        </div>
    </form>
```

### Server side Updat

```js
// Update form 
    app.put('/update-job/:id', async(req, res) => {
      const id = req.params.id
      const jobData = req.body
      const updated = {
        $set: jobData,
      }
      const query = {_id: new ObjectId(id)}
      const options = {upsert: true}
      const result = await jobsCollection.updateOne(query, updated,options )
      res.send(result)
    })
```