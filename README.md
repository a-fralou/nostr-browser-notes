# Solution description
1. Установка React.js и ts
2. Настройка Eslint, Prettier, Husky
3. Использование в проекте Dexie:
    1. [Документация к Dexie](https://dexie.org/)
2. Установка дополнительных пакетов таких как:
    1. Redux - тут стоит хорошо подумать, возможно достаточно контекста
    2. React-query
    3. Axios(fetch запросы)
    4. [ant design ](https://ant.design/) - для стилей
    5. [MUI design ](https://mui.com/) - для стилей (* вместо antd)
3. Настройка моковой среды: Express & Nest + swagger
    1. Установка Express & Nest
    2. Использование фейковых данных Faker.js
    3. Установка `swagger-jsdoc` и `swagger-ui-express` для документации API.

## Structure
```
src
  | - components
    | - Button
    | - Input
    | - Toggle
    | - Textarea
    | - Dropdown
    | - Tabs
    | - Tooltip
    | - ProgressBar
    | - Switcher
    | - Checkbox
    | - labels
    | - AuthenticationWrapper  
    | - Pin  
    | - Profile
    | - User    
      
  | - entities
      
  | - dtos    
    | - user    
    | - keys    
      
  | - pages
    | - sign-in
    | - main-page
    | - tab-page
    
  | - modules
    | - modals
      | - Modal
      | - ConfirmModal
      | - InfoModal
      | - SuccessModal

  | - containers
    | - common
    | - pages
        | - SignIn
        | - Tab
    
  | - layout
    
  | - helpers
    | - errors.helpers
    | - query.helpers
    | - dates.helpers
    | - format.helpers:
  
  | - hooks          
    | - form.hook
    | - modals.hook
    | - tooltip.hook
    
  | - validations
    | - common.validations
    
  | - services
    | - api  
```


### Auth guards
Все базовые табы будут обёрнуты `AuthenticationWrapper`, где проверяется текущий пользователь и показывается соответствующий ui:

### Работа с лейаутами
Будет один лейаут, где в зависимости от типа пользователя рендерится нужный ui:
```
const Layout = ({ children }) => {
    const user = useUser();
    
    return (
        <>
            {user && <SomeComponent />}
            <div>{children}</div>
        </>
    );
}

Use case: _app.tsx

return (
    <AuthenticationWrapper>
        <Layout>
            <Component {...pageProps} />
        </Layout>
    </AuthenticationWrapper>
);
```

### Работы с формами
Чтобы не дублировать каждый раз общий конфиг/логику формы, будет хук базовой формы:
`src/hooks/form.hook.ts`
```
Use case:

const { errors, handleSubmit ...other } = useForm({ validationSchema, ...customOptions });
```

Так же в ней можно сделать общую логику для всех форм. Например очистка статуса error поля если оно изменилось. По необходимости это будет конфигурироваться через параметры.

Если форма сложная - будет отдельный хук со специфической настройкой.

Если интерфейс request api отличается от интерфейса формы - будут реализованы адаптеры:
```
const handleSendForm = async (form: FormSchema) => {
    const body = prepareDataToCreatePass(form);
    await fetch(body);
}
```
Тоже самое и в обратную сторону, если нужна initialData с query в форму с отличным интерфейсом - делаем адаптер где скрываем всю работу.

### Работа с табами страниц
Их состояние и фоновую работу можно настроить через сервис воркер хук
```
Use case 1:
const { tab } = useTab();
const state = prepareTabInfo(tab);


Use case 2:
const handleApplyTab = async (rawData) => {
    const newQuery = prepareTabInfo(rawData);
    await fetch({ ..., query: newQuery })
}
```

### Хранение глоб. данных
По умолчанию используем react-query, все данные по get запросам будут храниться там.
Остальное состояние локально/context.
Стоит обдумать контекст вместо глобал стейт


## 3rd-party services
Не требуется