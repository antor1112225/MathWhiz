# ANTOR – MATH WHIZ ⚡
### Live Lab Test # 2: Java Web Application Development
- **Course**: ICT 3107 & ICT 3108
- **Student Name**: Md. Tahmid Hasib Antor
- **Student ID**: IT-24055
- **Total Marks**: 10 Marks
- **Technology Stack**: Java 11+, Jakarta EE 10 (Servlets & JSP), JDBC, MySQL 8.0, Apache Tomcat 10.1.x, Maven, JUnit 5
- **GitHub Repository**: [https://github.com/antor1112225/MathWhiz](https://github.com/antor1112225/MathWhiz)

---

## 📋 Task Breakdown & Implementation Details

---

### Task 01: Project Setup & Architecture
- **Web Technology**: Jakarta Servlet 6.0 and JSP 3.0 web application running on Apache Tomcat 10.1.x.
- **Build System**: Apache Maven (`pom.xml`) with compiler target set to Java 11+.
- **Database Driver**: MySQL Connector/J (`mysql-connector-java` 8.0.33) for JDBC connectivity.
- **Testing Framework**: JUnit 5 (`junit-jupiter` 5.10.2).

#### Project Structure:
```
MathWhiz/
├── database/
│   └── math_whiz_db.sql                   # MySQL database creation & schema script
├── screenshots/
│   ├── home.png                           # Task 02: Welcome page screenshot
│   ├── game.png                           # Task 02: Game module challenge screenshot
│   ├── result.png                         # Task 02: Results evaluation screenshot
│   └── leaderboard.png                    # Task 02: Top 10 leaderboard screenshot
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── controller/
│   │   │   │   ├── GameServlet.java        # Task 06: Question generator & game session controller
│   │   │   │   └── LeaderboardServlet.java # Task 08: Top 10 leaderboard controller
│   │   │   ├── dao/
│   │   │   │   └── ScoreDAO.java           # Task 07 & 08: JDBC PreparedStatement DAO
│   │   │   ├── model/
│   │   │   │   ├── Attempt.java            # Single question attempt model
│   │   │   │   ├── GameScore.java          # Score record DTO model
│   │   │   │   ├── GameState.java          # Session game state model
│   │   │   │   └── MathModule.java         # Module configuration & rubric marks
│   │   │   ├── service/
│   │   │   │   └── MathService.java        # Task 03, 04, 05: Core mathematical algorithms
│   │   │   └── util/
│   │   │       └── DBConnection.java       # Task 07: JDBC database connection provider
│   │   └── webapp/
│   │       ├── css/
│   │       │   └── style.css               # Modern glassmorphism styling
│   │       ├── WEB-INF/
│   │       │   └── web.xml                 # Web descriptor
│   │       ├── game.jsp                    # Task 02: Game interface
│   │       ├── index.jsp                   # Task 02: Welcome / start page
│   │       ├── leaderboard.jsp             # Task 02 & 08: Leaderboard page
│   │       └── result.jsp                  # Task 02: Result evaluation page
│   └── test/
│       └── java/
│           └── service/
│               └── MathServiceTest.java    # Task 09: Unit tests for MathService
├── pom.xml                                 # Maven project configuration
└── README.md                               # Project documentation
```

---

### Task 02: Game Interface Design
The user interface features a clean, responsive glassmorphism UI with gradient aesthetics:
1. **Welcome Page (`index.jsp`)**:
   - Header banner: `ANTOR – MATH WHIZ`.
   - Player name input with required field validation.
   - Information card showing rubric breakdown: Prime (3 marks), Modulus (3 marks), Trigonometry (4 marks).
   - "Start Game" and "Leaderboard" navigation buttons.
2. **Game Interface (`game.jsp`)**:
   - Active player status bar showing current score and maximum score.
   - Dynamic screens for module selection, prime number generation, modulus input, and trigonometric ratio calculation.
3. **Result Page (`result.jsp`)**:
   - Displays final score badge (e.g. `10 / 10`) and player name.
   - Detailed breakdown table comparing Module, Question, Player's Answer, Java's Real Answer, and Marks.
   - Status alert confirming MySQL persistence via JDBC.
4. **Leaderboard Page (`leaderboard.jsp`)**:
   - Top 10 scores sorted in descending order with Rank, Player Name, Score, Difficulty, and Timestamp.

---

### Task 03: Prime Number Function
- **Class**: `service.MathService`
- **Method**: `public static int isPrime(int number)`
- **Marks**: 3 Marks
- **Implementation**:
  - Returns `1` if the number is prime, `0` otherwise.
  - Numbers strictly below 2 (`number < 2`) return `0`.
  - Optimized $O(\sqrt{n})$ trial division.

```java
public static int isPrime(int number) {
    if (number < 2) {
        return 0;
    }
    if (number < 4) {
        return 1;
    }
    if (number % 2 == 0) {
        return 0;
    }
    for (long i = 3; i * i <= number; i += 2) {
        if (number % i == 0) {
            return 0;
        }
    }
    return 1;
}
```

---

### Task 04: Modulus Function
- **Class**: `service.MathService`
- **Method**: `public static int calculateMod(int a, int b)`
- **Marks**: 3 Marks
- **Implementation**:
  - Calculates remainder of integer division: `a % b`.
  - Zero divisor validation: throws `IllegalArgumentException` if divisor $b = 0$.

```java
public static int calculateMod(int a, int b) {
    if (b == 0) {
        throw new IllegalArgumentException("Divisor must not be zero.");
    }
    return a % b;
}
```

---

### Task 05: Trigonometric Functions
- **Class**: `service.MathService`
- **Methods**:
  - `public static double calculateSin(double degree)`
  - `public static double calculateCos(double degree)`
  - `public static double calculateTan(double degree)`
  - `public static boolean isClose(double expected, double actual)`
- **Marks**: 4 Marks
- **Implementation**:
  - Converts degree input to radians via `Math.toRadians(degree)`.
  - Handles undefined tangent values at $90^\circ, 270^\circ, \dots$ by throwing `ArithmeticException`.
  - Allows floating-point tolerance of $\pm 0.01$ (`TOLERANCE = 0.01`).

```java
public static double calculateSin(double degree) {
    return Math.sin(Math.toRadians(degree));
}

public static double calculateCos(double degree) {
    return Math.cos(Math.toRadians(degree));
}

public static double calculateTan(double degree) {
    if (isTanUndefined(degree)) {
        throw new ArithmeticException("tan is undefined.");
    }
    return Math.tan(Math.toRadians(degree));
}
```

---

### Task 06: Dynamic Question Generation & Game Session Flow
- **Class**: `controller.GameServlet`
- **Session Management**: Uses `HttpSession` (`session.getAttribute("state")`) to track:
  - Active player name
  - Played modules (`primeDone`, `modDone`, `trigDone`)
  - Running score and attempt history
- Prevents question tampering and enforces sequential completion.

---

### Task 07: Database Connectivity & Score Recording
- **Connection Helper**: `util.DBConnection.getConnection()`
- **DAO Class**: `dao.ScoreDAO`
- **Database**: MySQL 8.0 (`math_whiz_db.game_scores`)
- **Query**: Uses parameterized `PreparedStatement` to prevent SQL Injection:

```sql
INSERT INTO game_scores (player_name, score, difficulty, played_at)
VALUES (?, ?, ?, ?);
```

---

### Task 08: Top 10 Leaderboard
- **Controller**: `controller.LeaderboardServlet`
- **DAO Method**: `ScoreDAO.getTopScores(10)`
- **Query**:
```sql
SELECT id, player_name, score, difficulty, played_at
FROM game_scores
ORDER BY score DESC, played_at ASC
LIMIT 10;
```

---

### Task 09: Test Cases & Verification
- **Class**: `src/test/java/service/MathServiceTest.java`
- Tests prime detection, modulus operations, trigonometric values, zero divisor exceptions, and undefined tangent validation.
- Run via Maven:
```bash
mvn test
```

---

### Task 10: Application Screenshots & Deployment

#### Welcome Page
![Welcome Page](screenshots/home.png)

#### Game Interface
![Game Interface](screenshots/game.png)

#### Result Evaluation
![Result Page](screenshots/result.png)

#### Leaderboard
![Leaderboard](screenshots/leaderboard.png)

---

## 👨‍💻 Author
**Md. Tahmid Hasib Antor**  
Student ID: **IT-24055**  
Department of Information and Communication Technology
