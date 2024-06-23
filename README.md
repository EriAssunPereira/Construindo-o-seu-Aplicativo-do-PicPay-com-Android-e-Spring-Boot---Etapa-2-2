# Construindo-o-seu-Aplicativo-do-PicPay-com-Android-e-Spring Boot-Etapa-2-2

Para construir um aplicativo semelhante ao PicPay, que inclui funcionalidades como tela de pagamento para contatos, histórico de transações e edição de perfil do usuário, dividiremos o projeto em duas partes principais: o frontend utilizando Android Nativo com Kotlin e o backend com uma API REST usando Spring Boot e Java 8.

### Passos do Projeto

#### Parte 1: Backend com Spring Boot

1. **Configuração do Projeto Spring Boot**
   - Crie um novo projeto Spring Boot utilizando o Spring Initializr (https://start.spring.io/).
   - Adicione as dependências necessárias como `Spring Web`, `Spring Data JPA`, `Spring Security`, etc.

2. **Modelagem dos Dados**
   - Defina as entidades principais do sistema, como `User`, `Transaction`, `Payment`, etc.
   - Exemplo de entidade `User`:
     ```java
     @Entity
     public class User {
         @Id
         @GeneratedValue(strategy = GenerationType.IDENTITY)
         private Long id;
         
         @Column(unique = true)
         private String username;
         
         private String email;
         private String password;
         
         // getters e setters
     }
     ```

3. **Desenvolvimento da API REST**
   - Implemente endpoints para manipulação de usuários, transações, pagamentos, etc.
   - Exemplo de controller para usuários:
     ```java
     @RestController
     @RequestMapping("/api/users")
     public class UserController {
         
         @Autowired
         private UserService userService;
         
         @GetMapping("/{id}")
         public ResponseEntity<User> getUserById(@PathVariable Long id) {
             User user = userService.findById(id);
             if (user != null) {
                 return ResponseEntity.ok(user);
             } else {
                 return ResponseEntity.notFound().build();
             }
         }
         
         // outros métodos como createUser, updateUser, etc.
     }
     ```

4. **Segurança com Spring Security**
   - Configure autenticação e autorização usando Spring Security e JWT (JSON Web Tokens).
   - Exemplo de configuração básica de segurança:
     ```java
     @Configuration
     @EnableWebSecurity
     public class SecurityConfig extends WebSecurityConfigurerAdapter {
         
         @Autowired
         private JwtAuthenticationEntryPoint jwtAuthenticationEntryPoint;
         
         @Autowired
         private UserDetailsService jwtUserDetailsService;
         
         @Autowired
         private JwtRequestFilter jwtRequestFilter;
         
         @Autowired
         public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
             auth.userDetailsService(jwtUserDetailsService).passwordEncoder(passwordEncoder());
         }
         
         @Bean
         public PasswordEncoder passwordEncoder() {
             return new BCryptPasswordEncoder();
         }
         
         @Override
         @Bean(BeanIds.AUTHENTICATION_MANAGER)
         public AuthenticationManager authenticationManagerBean() throws Exception {
             return super.authenticationManagerBean();
         }
         
         @Override
         protected void configure(HttpSecurity httpSecurity) throws Exception {
             httpSecurity.csrf().disable()
                 .authorizeRequests().antMatchers("/api/authenticate", "/api/register").permitAll()
                 .anyRequest().authenticated().and()
                 .exceptionHandling().authenticationEntryPoint(jwtAuthenticationEntryPoint).and()
                 .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
             httpSecurity.addFilterBefore(jwtRequestFilter, UsernamePasswordAuthenticationFilter.class);
         }
     }
     ```

5. **Persistência de Dados**
   - Utilize o Spring Data JPA para persistir os dados no banco de dados (por exemplo, MySQL, PostgreSQL).
   - Configure o arquivo `application.properties` com as configurações de banco de dados.

6. **Testes e Documentação da API**
   - Escreva testes unitários e de integração para garantir o funcionamento correto da API.
   - Documente a API usando ferramentas como Swagger para facilitar a integração com o frontend.

#### Parte 2: Frontend com Android Nativo e Kotlin

1. **Configuração do Projeto Android**
   - Crie um novo projeto Android usando o Android Studio.
   - Configure as dependências necessárias no arquivo `build.gradle`.

2. **Implementação das Telas**
   - Desenvolva as telas principais do aplicativo, como tela de login, tela de perfil, tela de histórico de transações, tela de pagamentos, etc.
   - Utilize `Activities` e `Fragments` conforme necessário.

3. **Integração com API REST**
   - Implemente classes para realizar requisições HTTP para os endpoints da API REST desenvolvida com Spring Boot.
   - Exemplo de classe para requisição HTTP usando Retrofit:
     ```kotlin
     interface ApiService {
         @GET("/api/users/{id}")
         suspend fun getUserById(@Path("id") id: Long): Response<User>
         
         // outros métodos como createUser, updateUser, etc.
     }
     ```

4. **Gerenciamento de Estado com ViewModel e LiveData**
   - Utilize `ViewModels` e `LiveData` para gerenciar o estado da aplicação e atualizações na UI de forma reativa.
   - Exemplo de ViewModel:
     ```kotlin
     class UserViewModel(application: Application) : AndroidViewModel(application) {
         private val userRepository = UserRepository()
         private val _user = MutableLiveData<User>()
         val user: LiveData<User>
             get() = _user
         
         fun fetchUserById(id: Long) {
             viewModelScope.launch {
                 val response = userRepository.getUserById(id)
                 if (response.isSuccessful) {
                     _user.postValue(response.body())
                 } else {
                     // tratamento de erro
                 }
             }
         }
     }
     ```

5. **Estilização e Layouts**
   - Utilize XML para definir layouts e recursos visuais do aplicativo.
   - Mantenha a consistência de design conforme as diretrizes de Material Design.

6. **Autenticação e Segurança**
   - Implemente a tela de login e utilize tokens JWT para autenticar as requisições na API REST.

7. **Testes e Publicação**
   - Teste o aplicativo em diferentes dispositivos e emuladores para garantir a compatibilidade.
   - Prepare o aplicativo para publicação na Google Play Store, seguindo as diretrizes de publicação.

### Considerações Finais

Construir um aplicativo estilo PicPay envolve integração entre frontend e backend de forma eficiente, garantindo segurança, performance e uma boa experiência de usuário. Certifique-se de seguir boas práticas de desenvolvimento em ambas as plataformas, documente o código e teste regularmente para evitar problemas durante o desenvolvimento e após a publicação do aplicativo.
