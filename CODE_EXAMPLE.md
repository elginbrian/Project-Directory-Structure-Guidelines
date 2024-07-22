## UserDao.kt

```kotlin
import androidx.room.Dao
import androidx.room.Insert
import androidx.room.Query
import kotlinx.coroutines.flow.Flow

@Dao
interface UserDao {
    @Query("SELECT * FROM user WHERE id = :id")
    fun getUserById(id: String): Flow<UserEntity>

    @Insert
    suspend fun insertUser(user: UserEntity)
}

```

## AppDatabase.kt

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase

@Database(entities = [UserEntity::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}

```

## UserLocalDataSource.kt

```kotlin
class UserLocalDataSource(private val userDao: UserDao) {
    fun getUserById(id: String) = userDao.getUserById(id)

    suspend fun insertUser(user: UserEntity) = userDao.insertUser(user)
}

```

## UserService.kt

```kotlin
import retrofit2.http.GET
import retrofit2.http.Path

interface UserService {
    @GET("users/{id}")
    suspend fun getUser(@Path("id") id: String): UserDto
}

```

## UserRemoteDataSource.kt

```kotlin
class UserRemoteDataSource(private val userService: UserService) {
    suspend fun getUser(id: String) = userService.getUser(id)
}

```

## UserRepositoryImpl.kt

```kotlin
class UserRepositoryImpl(
    private val userLocalDataSource: UserLocalDataSource,
    private val userRemoteDataSource: UserRemoteDataSource,
    private val userMapper: UserMapper
) : UserRepository {

    override suspend fun getUser(id: String): User {
        // Check local data source
        val userEntity = userLocalDataSource.getUserById(id).firstOrNull()
        return userEntity?.let { userMapper.toUser(it) } ?: run {
            // Fetch from remote data source
            val userDto = userRemoteDataSource.getUser(id)
            val user = userMapper.toUser(userDto)
            // Save to local data source
            userLocalDataSource.insertUser(userMapper.toUserEntity(user))
            user
        }
    }
}

```

## UserMapper.kt

```kotlin
class UserMapper {
    fun toUser(userDto: UserDto): User {
        return User(userDto.id, userDto.name)
    }

    fun toUserEntity(user: User): UserEntity {
        return UserEntity(user.id, user.name)
    }
}

```

## Module.kt

```kotlin
import org.koin.dsl.module

val appModule = module {
    single { Room.databaseBuilder(get(), AppDatabase::class.java, "app_database").build() }
    single { get<AppDatabase>().userDao() }
    single { UserLocalDataSource(get()) }
    single { Retrofit.Builder().baseUrl("https://api.example.com/").build().create(UserService::class.java) }
    single { UserRemoteDataSource(get()) }
    single { UserMapper() }
    single { UserRepositoryImpl(get(), get(), get()) as UserRepository }
}

```

## User.kt

```kotlin
data class User(val id: String, val name: String)

```

## UserRepository.kt

```kotlin
class GetUserUseCase(private val userRepository: UserRepository) {
    suspend fun execute(id: String): User {
        return userRepository.getUser(id)
    }
}

```

## GetUserUseCase.kt

```kotlin
class GetUserUseCase(private val userRepository: UserRepository) {
    suspend fun execute(id: String): User {
        return userRepository.getUser(id)
    }
}

```

## UserScreen.kt

```kotlin
@Composable
fun UserScreen(viewModel: UserViewModel) {
    val user by viewModel.user.observeAsState()
    user?.let {
        Text(text = "User: ${it.name}")
    }
}

```

## UserDetailScreen.kt

```kotlin
@Composable
fun UserScreen(viewModel: UserViewModel) {
    val user by viewModel.user.observeAsState()
    user?.let {
        Text(text = "User: ${it.name}")
    }
}

```

## UserListScreen.kt

```kotlin
@Composable
fun UserScreen(viewModel: UserViewModel) {
    val user by viewModel.user.observeAsState()
    user?.let {
        Text(text = "User: ${it.name}")
    }
}

```

## UserViewModel.kt

```kotlin
class UserViewModel(private val getUserUseCase: GetUserUseCase) : ViewModel() {
    private val _user = MutableLiveData<User>()
    val user: LiveData<User> get() = _user

    fun getUser(id: String) {
        viewModelScope.launch {
            _user.value = getUserUseCase.execute(id)
        }
    }
}

```

## Color.kt

```kotlin
import androidx.compose.ui.graphics.Color

val PrimaryColor = Color(0xFF6200EE)
val SecondaryColor = Color(0xFF03DAC5)

```

## Typography.kt

```kotlin
import androidx.compose.material.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily

val Typography = Typography(
    h1 = TextStyle(fontFamily = FontFamily.Default, fontWeight = FontWeight.Bold, fontSize = 30.sp),
    // Add other text styles as needed
)

```

## Theme.kt

```kotlin
import androidx.compose.material.MaterialTheme
import androidx.compose.material.darkColors
import androidx.compose.runtime.Composable
import androidx.compose.ui.graphics.Color

private val DarkColorPalette = darkColors(
    primary = PrimaryColor,
    secondary = SecondaryColor
)

@Composable
fun AppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colors = DarkColorPalette,
        typography = Typography,
        content = content
    )
}

```

## CustomWidget.kt

```kotlin
@Composable
fun CustomWidget(text: String) {
    Text(text = text, color = Color.White, modifier = Modifier.padding(16.dp))
}

```

## NavGraph.kt

```kotlin
import androidx.compose.runtime.Composable
import androidx.navigation.NavHostController
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable

@Composable
fun NavGraph(navController: NavHostController) {
    NavHost(navController = navController, startDestination = "auth") {
        composable("auth") { AuthNavHost(navController) }
        composable("home") { HomeNavHost(navController) }
    }
}

```

## AuthNavHost.kt

```kotlin
@Composable
fun AuthNavHost(navController: NavHostController) {
    NavHost(navController = navController, startDestination = "login") {
        composable("login") { LoginScreen() }
        // Add other authentication related composable destinations
    }
}

```

## HomeNavHost.kt

```kotlin
@Composable
fun HomeNavHost(navController: NavHostController) {
    NavHost(navController = navController, startDestination = "home") {
        composable("home") { HomeScreen() }
        // Add other home related composable destinations
    }
}

```

## Tools.kt

```kotlin
object Tools {
    fun formatDate(date: LocalDate): String {
        return date.format(DateTimeFormatter.ofPattern("yyyy-MM-dd"))
    }
}

```
