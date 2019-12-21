#Before
Leaks memory
```typescript
describe('AppComponent', () => {
  const fixture: ComponentFixture<AppComponent>;
  const component: AppComponent;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [
        AppComponent,
      ],
      schemas: [
        CUSTOM_ELEMENTS_SCHEMA
      ]
    }).compileComponents();
    fixture = TestBed.createComponent(AppComponent);
    component = fixture.componentInstance;
  });

  it('should create the app', () => {
    expect(component).toBeTruthy();
  });
  
  describe('testFunction', () => {
     const testString: string;
     beforeEach(() => {
        testString = 'open the pod bay doors, hal';
     });
     it('should return something from hal', () => {
        const response: string = 'i\'m sorry dave';
        expect(await component.testFunction(testString)).toEqual(response);
     });
  });
});
```
#After
Does not leak memory

It is okay to have variable inside of `it` statement as only shared variables can leak
```typescript
describe('AppComponent', () => {
  interface AppComponentTests {
    component: AppComponent;
    fixture: ComponentFixture<AppCompent>;
  }

  beforeEach(async function (this: AppComponentTests) {
    await TestBed.configureTestingModule({
      declarations: [
        AppComponent,
      ],
      schemas: [
        CUSTOM_ELEMENTS_SCHEMA
      ]
    }).compileComponents();
    this.fixture = TestBed.createComponent(AppComponent);
    this.component = this.fixture.componentInstance;
  });

  it('should create the app', function (this: AppComponentTests) {
    expect(this.component).toBeTruthy();
  });
  
  describe('testFunction', () => {
     interface TestFunctionTests extends AppComponentTests {
         testString: string;
     }
     beforeEach(function (this: TestFunctionTests) {
        this.testString = 'open the pod bay doors, hal';
     })
     it('should return something from hal', function (this: TestFunctionTests) {
        const response: string = 'i\'m sorry dave';
        expect(await this.component.testFunction(this.testString)).toEqual(response);
     });
  });
});
```
