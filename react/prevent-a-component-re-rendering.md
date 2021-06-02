# Prevent re-renders in Function Components

## React memo

For a `FunctionComponent` we have to use `React.memo` to prevent a re-render. This is a basic example showing showing a list of appointments

```javascript
const AppointmentItem = (props) => (
  <div>
    <span>{props.place}</span>
    <span>{props.time}</span>
  </div>
);
```

The above example will re-render whenever the object reference for `props` is changed. This is common if say a parent component re-renders due to a change to the list of `Appointments`.

To prevent this the component can be wrapped in `React.memo`:

```javascript
const AppointmentItem = React.memo((props) => (
  <div>
    <span>{props.place}</span>
    <span>{props.time}</span>
  </div>
));
```

This will memoize the result of calling `AppointmentList` with the current props. Internally react will perform a shallow compare on the current and next props. This means it will first check if props objects have the same reference, if they are different it will then check equality for each key in the current and next props. If all keys are equal it will bail out of the render update for this component.

## Callbacks

A common pitfall with managing updates in this way is passing callback to a component

```javascript
const Appointments = props => {
    const [appointmentList, setAppointmentList] = useState([]);

    const deleteAppointment = id => setAppointmentList(state => state.filter(appointment => appointment.id !== id))

    (
    <div>
        {appointmentList.map((appointment) => {
            (<AppointmentItem
                onDelete={() => deleteAppointment(appointment.id)}
                {...appointment}
            >)
        }}
    </div>
)}
```

The `deleteAppointment` function will be re-created every time the `Appointments` component renders which is whenever the `appointmentList` state changes. This will fail the check in `React.memo`. 

To fix this change the function to use `useCallback`:


```javascript
const Appointments = props => {
    const [appointmentList, setAppointmentList] = useState([]);

    const deleteAppointment = useCallback(id => setAppointmentList(state => state.filter(appointment => appointment.id !== id)), []);

    (
    <div>
        {appointmentList.map((appointment) => {
            (<AppointmentItem
                onDelete={() => deleteAppointment(appointment.id)}
                {...appointment}
            >)
        }}
    </div>
)}
```
