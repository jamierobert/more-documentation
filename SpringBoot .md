# SpringBoot 

### Testing

#### Annotations

- `@RunWith(SpringRunner.class)` - could also use `SpringJUnit4ClassRunner.class`
- `@SpringBootTest()` - Dont always need to provide classes but it is good practice for readability.

    Optional Paramenters
    - `classes = {Start.class, CUT.class}`
    - `webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT`
    
- `@DirtiesContext`
- `@ContextConfiguration(initializers = BaseIT.Initializer.class)`

#### Patterns

##### Initializing the application context.

    public static class Initializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {
        @Override
        public void initialize(final ConfigurableApplicationContext configurableApplicationContext) {
            TestPropertyValues.of("kafka.brokers=" + kafka.getBootstrapServers())
                    .and("ws.maxConnectionLimit=" + MAX_CONNECTIONS)
                    .and("ws.maxMessagesPerSession=" + MAX_MESSAGES)
                    .and("topic.validation.regex=" + TOPIC)
                    .and("correlation.id.validation.regex=" + VALID_CORRELATION_ID)
                    .applyTo(configurableApplicationContext);
        }
    }

### Core

#### Annotations

- `@Autowired` -
- `@Bean`- 

### Data

###### JPA
- `@Entity`
- `@Id`
- `@GeneratedValue`
- `@Table`
- `@Column`

##### JPA REPOSITORY METHODS

`repository.findAll()` - 
`repository.findById()` - 
`repository.save()` - 