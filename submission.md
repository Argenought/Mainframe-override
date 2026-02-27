### 1. Get the username of the person who made the post about EmptyStack in forum_posts.

- `\d` to see a list of all tables in the database
  ```bash
        Schema |        Name         | Type  | Owner
  --------+---------------------+-------+-------
  public | emptystack_accounts | table | Pedro
  public | forum_accounts      | table | Pedro
  public | forum_posts         | table | Pedro
  (3 rows)
  ```
- We are looking for a post made in forum_posts.
- `\d forum_posts` to see whats in side that table
  ```bash
    Column  |              Type              | Collation | Nullable | Default
    ---------+--------------------------------+-----------+----------+---------
    id      | text                           |           | not null |
    title   | text                           |           | not null |
    content | text                           |           | not null |
    date    | timestamp(3) without time zone |           | not null |
    author  | text                           |           | not null |
    Indexes:
        "forum_posts_pkey" PRIMARY KEY, btree (id)
  ```
- `SELECT * FROM forum_posts WHERE content ILIKE '%emptystack%';` (Note rember to use '' not ``)
  - Doesn't narrow it down enough
- Forgot there was a clue
  - date: April 2048
  - content: empty stack and dad
- `SELECT date FROM forum_posts;` to check how the date is formated
- `SELECT * FROM forum_posts WHERE date BETWEEN '2048-04-01' AND '2048-05-01' AND content ILIKE '%emptystack%' AND content ILIKE '%dad%';`
  ```bash
      id   |     title     |                                                                                              content
                              |        date         |     author
    -------+---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    --------------------------+---------------------+----------------
    nbZY_ | Get rich fast | You should all invest in EmptyStack Solutions soon or you'll regret it. My dad works there and he's got some serious inside intel. Their self-driving taxis are the futur
    e and the future is here. | 2048-04-08 00:00:00 | smart-money-44
    (1 row)
  ```
- The author is `smart-money-44`

### 2. Get the last name of the person associated with that username in forum_accounts.

- `\d forum_accounts`
  ```bash
      Column   | Type | Collation | Nullable | Default
    ------------+------+-----------+----------+---------
    username   | text |           | not null |
    first_name | text |           | not null |
    last_name  | text |           | not null |
    Indexes:
        "forum_accounts_pkey" PRIMARY KEY, btree (username)
        "forum_accounts_username_key" UNIQUE, btree (username)
  ```
- `SELECT * FROM forum_accounts WHERE username = 'smart-money-44';`
  ```bash
        username    | first_name | last_name
    ----------------+------------+-----------
    smart-money-44 | Brad       | Steele
    (1 row)
  ```
- The last name is `Steele`

### 3. Find all other accounts with the same last name.

- `SELECT * FROM forum_accounts WHERE last_name = 'Steele';`
  ```bash
        username     | first_name | last_name
    -----------------+------------+-----------
    sharp-engine-57 | Andrew     | Steele
    stinky-tofu-98  | Kevin      | Steele
    smart-money-44  | Brad       | Steele
    (3 rows)
  ```

### 4. Find all accounts in emptystack_accounts with the same last name.

- `\d emptystack_accounts`
  ```bash
      Column   | Type | Collation | Nullable | Default
    ------------+------+-----------+----------+---------
    username   | text |           | not null |
    password   | text |           | not null |
    first_name | text |           | not null |
    last_name  | text |           | not null |
    Indexes:
        "emptystack_accounts_pkey" PRIMARY KEY, btree (username)
        "emptystack_accounts_username_key" UNIQUE, btree (username)
  ```
- `SELECT * FROM emptystack_accounts WHERE last_name = 'Steele';`
  ```bash
        username    |  password   | first_name | last_name
    ----------------+-------------+------------+-----------
    triple-cart-38 | password456 | Andrew     | Steele
    lance-main-11  | password789 | Lance      | Steele
    (2 rows)
  ```

### 5. There will only be one EmptyStack employee with a forum account. Use their credentials to access node mainframe, which will output a new sql file for you to run.

- The only match is `Andrew Steele`
  - username: `triple-cart-38`
  - password: `password456`
- Open new terminal window, run node mainframe, enter credentials.
- Update database, get new tables
  ```bash
    Schema |        Name         | Type  | Owner
    --------+---------------------+-------+-------
    public | emptystack_accounts | table | Pedro
    public | emptystack_messages | table | Pedro
    public | emptystack_projects | table | Pedro
    public | forum_accounts      | table | Pedro
    public | forum_posts         | table | Pedro
    (5 rows)
  ```

### 6. Find the message in emptystack_messages that mentions a project involving self-driving taxis. That message is sent from an admin account and also reveals the project code.

- `\d emptystack_messages;`
  ```bash
    Column  | Type | Collation | Nullable | Default
    ---------+------+-----------+----------+---------
    id      | text |           | not null |
    from    | text |           | not null |
    to      | text |           | not null |
    subject | text |           | not null |
    body    | text |           | not null |
    Indexes:
        "emptystack_messages_pkey" PRIMARY KEY, btree (id)
  ```
- `SELECT * FROM emptystack_messages WHERE body ILIKE '%self-driving%';`
  - Nothing
- `SELECT * FROM emptystack_messages WHERE body ILIKE '%taxi%';`
  ```bash
      id   |     from     |       to       |   subject    |                            body
    -------+--------------+----------------+--------------+------------------------------------------------------------
    LidWj | your-boss-99 | triple-cart-38 | Project TAXI | Deploy Project TAXI by end of week. We need this out ASAP.
    (1 row)
  ```
- Admin account is `your-boss-99`

### 7. Get the credentials for the admin account from emptystack_accounts.

- `SELECT * FROM emptystack_accounts WHERE username = 'your-boss-99';`
  ```bash
      username   |    password    | first_name | last_name
    --------------+----------------+------------+-----------
    your-boss-99 | notagaincarter | Skylar     | Singer
    (1 row)
  ```

### 8. Get the ID of the project from emptystack_projects.

- `\d emptystack_projects`
  ```bash
    Column | Type | Collation | Nullable | Default
    --------+------+-----------+----------+---------
    id     | text |           | not null |
    code   | text |           | not null |
    Indexes:
        "emptystack_projects_pkey" PRIMARY KEY, btree (id)
  ```
- `SELECT * FROM emptystack_projects WHERE code ILIKE 'TAXI';`
  ```bash
        id    | code
    ----------+------
    DczE0v2b | TAXI
    (1 row)
  ```

### 9. Use that information to stop the project: node mainframe -stop!

- Enter the credentials will recieve message that project has been shutdown
