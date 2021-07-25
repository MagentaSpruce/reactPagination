# reactPagination
This React app takes a list of GitHub users and paginates them on the clientside.

This project utilizes the GitHub API to fetch relevant data.: https://api.github.com/users/elon-musk/followers?per_page=100

A general walkthrough of the pertinent React code is given below.

After the initial file tree has been setup, useFetch is created to retreive the data from the API.
```React
export const useFetch = () => {
  const [loading, setLoading] = useState(true)
  const [data, setData] = useState([])

  const getProducts = async () => {
    const response = await fetch(url)
    const data = await response.json()
    setData(data)
    setLoading(false)
  }

  useEffect(() => {
    getProducts()
  }, [])
  return { loading, data }
}
```

Now the list of users needs to be displayed to the page.
```React
        <div className="container">
          {data.map((follower) => {
            return <Follower key={follower.id} {...follower} />;
          })}
        </div>
```

Next destructuring is used to pull desired data from the GitHub API inside of Follower.js to display the cards and names/images.
```React
const Follower = ({ avatar_url, html_url, login }) => {
  return (
    <article className="card">
      <img src={avatar_url} alt={login} />
      <h4>{login}</h4>
      <a href={html_url} className="btn">
        View Profile
      </a>
    </article>
  );
};
```

Next the data is paginated to only display a certain amount of users at a time. Need to create arrays inside of arrays - specific arrays get displayed determined on the button chosen. First the paginate function is imported into the custom hook.
```React
  const getProducts = async () => {
    const response = await fetch(url);
    const data = await response.json();
    paginate(data);
...
```

Next the paginate function inside of paginate.js is worked upon.
const paginate = (followers) => {
  const itemsPerPage = 10;
  const pages = Math.ceil(followers.length / itemsPerPage);
  console.log(pages);
};
```


Next the array of arrays is created using Array.from() which will create the new array with the amount of items specified by the length - so in the case a new array containing 10 undefined items.
```React
  const newFollowers = Array.from({ length: pages });
  console.log(newFollowers);
```

In each of the undefined items, 10 followers need to be passed into each - this is done using start which is the value used to pull out the followers from the main list.
```React
  const newFollowers = Array.from({ length: pages }, (_, index) => {
    const start = index * itemsPerPage;
    console.log(start);
```

Next slice is used to pull items from the original array and set up the new arrays with the correct number of items.
```React
  const newFollowers = Array.from({ length: pages }, (_, index) => {
    const start = index * itemsPerPage;
    return followers.slice(start, start + itemsPerPage);
  });
```

Now the array of arrays is set up and working the goal is to make only one display on the page at a time.
```React
   setData(paginate(data));
```

This results in empty cards. To fix this in App.js useStates are set up.
```React
  const [page, setPage] = useState(0);
  const [followers, setFollowers] = useState([]);
```

Then a useEffect is set up so that once the app loads a certain page from the entire list is grabbed and displayed to the screen.
```React
  useEffect(() => {
    setFollowers(data[page]);
  }, []);
```

This results in  map undefined error bc initially the data is an empty array - to fix this loading is checked for so that the array is not checked until after loading has occurred.
```React
  useEffect(() => {
    if (loading) return;
    setFollowers(data[page]);
  }, []);
```

To prevent an empty screen the dependecy array needs to be set so that whenever loading changes the callback function is ran.
```React
  useEffect(() => {
    if (loading) return;
    setFollowers(data[page]);
  }, [loading]);
```


Next the display buttons that control which pages are displayed are conditionally rendered.
```React
        {!loading && <div className="btn-container">hello beebee</div>}
```

Next the data is iterated over to display one button for each page pagination.
```React
        </div>
        {!loading && (
          <div className="btn-container">
            {data.map((item, index) => {
              return (
                <button key={index} className="page-btn">
                  button
                </button>
              );
            })}
          </div>
```

Now fix the buttons so that each button displays the page that it corresponds to.
```React
                <button key={index} className="page-btn">
                 {index + 1}
                </button>
```

Now a function is set up to control which page is being shown.
```React
  const handlePage = (index) => {
    setPage(index);
  };
```

Now set the buttons up to run the handlePage function on click events.
```React
                <button
                  key={index}
                  className="page-btn"
                  onClick={() => handlePage(index)}
                >
                  {index + 1}
                </button>
 ```
 
 To make the above code work page needs to be added to the dependency array in useEffect to be run everytime the page changes.
 ```React
   useEffect(() => {
    if (loading) return;
    setFollowers(data[page]);
  }, [loading, page]);
```

Now the pagination is working. Next an active button is added dynamically using class names.
```React
               <button
                  key={index}
                  className={`page-btn ${index === page ? "active-btn" : null}`}
                  onClick={() => handlePage(index)}
                >
                  {index + 1}
                </button>
```

Next the prev and next buttons are added in.
```React
            <button className="prev-btn" onClick={prevPage}>
              prev
            </button>
            <button className="next-btn" onClick={nextPage}>
              next
            </button>
            
  const nextPage = () => {
    setPage((oldPage) => {
      let nextPage = oldPage + 1;
      if (nextPage > data.length - 1) {
        nextPage = 0;
      }
      return nextPage;
    });
  }; 
  
    const prevPage = () => {
    setPage((oldPage) => {
      let prevPage = oldPage - 1;
      if (prevPage < 0) {
        prevPage = data.length - 1;
      }
      return prevPage;
    });
  };
```

***End walkthrough
