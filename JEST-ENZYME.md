# Jest Enzyme tests

```JavaScript
import React from "react";
import ReactDOM from "react-dom";
import { shallow, mount, render } from "enzyme";
import fetchMock from "fetch-mock";
import { act } from "react-test-renderer";
import { renderHook } from "@testing-library/react-hooks";
import "whatwg-fetch";
import useFetch from "../Hooks/useFetch";
import App from "../components/App";
import {
  AppContainer,
  Heading,
  Loader,
  Error,
} from "../components/StyledComponents/StyledComponents";

jest.mock("../Hooks/useFetch");

describe("Testing App.js", () => {
  it("Renders without crashing", () => {
    useFetch.mockReturnValue([[], false, false]);
    const div = document.createElement("div");
    ReactDOM.render(<App />, div);
    shallow(<App />);
  });

  it("Renders a title", () => {
    useFetch.mockReturnValue([[], false, false]);
    const wrapper = shallow(<App />);
    expect(wrapper.text().includes("Bud - 10 Smallest Expenses")).toBe(true);

    expect(
      wrapper.containsMatchingElement(<Heading>Bud - 10 Smallest Expenses</Heading>)
    ).toEqual(true);
  });
});

describe("Testing async functionality", () => {
  it("It warns the user when fetch fails", () => {
    useFetch.mockReturnValue([null, false, true]);
    const wrapper = shallow(<App />);
    expect(
      wrapper.containsMatchingElement(<Error>Oops, there was a problem!</Error>)
    ).toEqual(true);
  });

  it("It displays a loader when the fetch request is loading", () => {
    useFetch.mockReturnValue([null, true, false]);
    const wrapper = shallow(<App />);
    expect(wrapper.containsMatchingElement(<Loader />)).toEqual(true);
  });
});
```

```JavaScript
  it("Loading state should go false -> true -> false from initialization to fetch request completion", async () => {
    fetchMock.mock("test1.com", {
      returnedData: "foo",
    });
    let response;
    // await act(async () => {
    //   // const { result } = renderHook(() => useFetch("test1.com"));
    //   // response = result;
    // });
    await act(async () => {
      const { result } = renderHook(() => useFetch("test.com"));
      response = result;
    });
  //   // loading begins as false
  //   expect(response.all[0]).toEqual([
  //     null,
  //     false,
  //     false,
  //   ]);
  //   expect(response.all[1]).toEqual([
  //     null,
  //     true,
  //     false,
  //   ]);
  //   expect(response.all[2]).toEqual([
  //     {
  //       returnedData: "foo",
  //     },
  //     false,
  //     false,
  //   ]);
  });

    it("Loading state should go false -> true -> false from initialization to fetch request completion", async () => {
    console.log('inside test 4');
    fetchMock.mock("test3.com", { result: 5 });
    let response;
    await act(async () => {
      const { result } = renderHook(() => useFetch("test3.com"));
      response = result;
    });
    expect(response.all[0]).toEqual([null, false, false]);
    expect(response.all[1]).toEqual([null, true, false]);
    expect(response.all[2]).toEqual([{ result: 5 }, false, false]);
  });
```