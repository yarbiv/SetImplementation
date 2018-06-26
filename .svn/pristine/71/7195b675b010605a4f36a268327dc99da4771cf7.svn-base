#include "tuple.h"
#include "set.h"
#include "invariant.h"
#include <iostream>
#include <limits.h>   // needed for INT_MIN

// Set up an error set to be returned as necessary
const Set emptySet(0,(int*)0);
const Set errorSet(-1,(int*)0);

bool Set::isEmpty() const {
  return _numElements == 0;
}

bool Set::isError() const {
  return _numElements == -1;
}

int Set::cardinality() const {
  return _numElements;
}

Set Set::union_(const Set& s) const {

  int size = s._tupleArraySize + _tupleArraySize;
  Tuple* tupleArray = new Tuple[size];
  int elementCount = 0;
  for (int i = 0; i < _numElements; i++) {
    tupleArray[elementCount++] = _pTuples[i];
  }
  for (int i = 0; i < s._numElements; i++) {
    tupleArray[elementCount++] = s._pTuples[i];
  }
  Set returnSet(elementCount, tupleArray);
  return returnSet;
}

Set Set::intersection(const Set& s) const {
  int size = s._numElements > _numElements ? _numElements : s._numElements;
  Tuple* tupleArray = new Tuple[size];
  int elementCount = 0;
  for (int i = 0; i < _numElements; i++) {
    for (int j = 0; j < s._numElements; j++) {
      if (_pTuples[i] == s._pTuples[j]) tupleArray[elementCount++] = _pTuples[i];
      //_pTuples[i] == s._pTuples[j] ? tupleArray[elementCount++] = _pTuples[i] : ;
    }
  }
  Set returnSet(elementCount, tupleArray);
  return returnSet;
}

Set Set::difference(const Set& s) const {
  int size = _numElements * s._numElements;
  Tuple* tupleArray = new Tuple[size];
  int elementCount = 0;
  for (int i = 0; i < _numElements; i++) {
    for (int j = 0; j < s._numElements; j++) {
      if (_pTuples[i] == s._pTuples[j]) break; //found common element, try next element
      if (j + 1 == s._numElements) tupleArray[elementCount++] = _pTuples[i]; //no common element, copy current element and move on
    }
  }
  Set returnSet = elementCount == 0 ? Set() : Set(elementCount, tupleArray);
  return returnSet;
}

Set Set::select(predicate* p) const {
  int size = _numElements;
  Tuple* tupleArray = new Tuple[size];
  int elementCount = 0;

  for (int i = 0; i < _numElements; i++) {
    if ( p(_pTuples[i]) ) tupleArray[elementCount++] = _pTuples[i];
  }
  Set returnSet(elementCount, tupleArray);
  return returnSet;
}

Set Set::operator()(const int item) const {
  if (item <= 0 || item > _numElements) return errorSet;

  Tuple* tupleArray = new Tuple[1];
  tupleArray[0] = _pTuples[item-1];
  Set returnSet(1, tupleArray);
  return returnSet;
}

Set Set::project(const int numItems, const int items[]) const {
  if (numItems > _tupleArraySize) return errorSet;
  if (numItems < 0 || _numElements < 0) return errorSet;

  int size = _numElements;
  Tuple* tupleArray = new Tuple[size];
  int elementCount = 0;
  for (int i = 0; i < _numElements; i++) {
    if (_pTuples[i].project(numItems, items).size() != INT_MIN) {
    tupleArray[i] = _pTuples[i].project(numItems, items);
    elementCount++;
    }
  }
  Set returnSet(elementCount, tupleArray);
  return returnSet;
}

Set Set::cartesian(const Set& s) const {
  int size = s._numElements * _numElements;
  Tuple* tupleArray = new Tuple[size];
  int elementCount = 0;

  for (int i = 0; i < s._numElements; i++) {
    for (int j = 0; j < _numElements; j++) {
      tupleArray[elementCount++] = s._pTuples[i] + _pTuples[j];
    }
  }
  Set returnSet(elementCount, tupleArray);
  return returnSet;
  //return emptySet;
}

void Set::operator=(const Set& s) {
  _numElements = s._numElements;
  _tupleArraySize = s._tupleArraySize;
  _pTuples = new Tuple[50];
  for (int i = 0; i < _numElements; i++) {
    _pTuples[i] = s._pTuples[i];
  }
}

Set::Set() {
  _numElements = 0;
  _tupleArraySize = 0;
  _pTuples = NULL;
}

Set::Set(const Set& s) {
  _numElements = s._numElements;
  _tupleArraySize = s._tupleArraySize;
  _pTuples = new Tuple[50];
  for (int i = 0; i < _numElements; i++) {
    _pTuples[i] = s._pTuples[i];
  }
}

Set::Set(const int numElements, const int data[]) {
  if (numElements < 0) {
    return;
  }

  _numElements = numElements;
  _tupleArraySize = numElements;

  bool dupeFound = false;
  int elementCount = 0;

  _pTuples = new Tuple[_tupleArraySize];
  for (int i = 0; i < _numElements; i++) {
    dupeFound = false;
    for (int j = 0; j < i; j++) {
      if (data[i] == data[j]) {
        dupeFound = true;
        break;
      }
    }
    if (!dupeFound) _pTuples[elementCount++] = Tuple(data[i]);
  }
  _numElements = elementCount;

}

Set::Set(const int numElements, const Tuple tuples[]) { //add dupe handling

  if (numElements < 0) {
    return;
  }

  _numElements = numElements;
  _tupleArraySize = numElements;

  bool dupeFound = false;
  int elementCount = 0;

  _pTuples = new Tuple[_tupleArraySize];
  for (int i = 0; i < _numElements; i++) {
    dupeFound = false;
    for (int j = 0; j < i; j++) {
      if (tuples[i] == tuples[j]) {
        dupeFound = true;
        break;
      }
    }
    if (!dupeFound && !tuples[i].isEmpty()) _pTuples[elementCount++] = tuples[i];
  }
  _numElements = elementCount;
}

Set::~Set() {
  delete[] _pTuples;
}

std::ostream& operator<<(std::ostream& os, const Set& s) {

  if (s._numElements == -1) {
    os << "This set has size -1: breaking function.";
    return os;
  }

  os << "{";

  for (int i = 0; i < s._numElements; i++) { //loops through tuples
    os << s._pTuples[i];
    if (i+1 < s._numElements) os << ","; //adds comma unless the next i element is the last element
  }
  os << "}";
  return os;
}
