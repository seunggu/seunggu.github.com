exports.testHistory = function(req, res) {
  Contract.findOne({
    attributes: ['contractPDFUrl', 'contractPDFS3Key', 'title', 'status', 'currentParticipantId',
      'currentParticipationOrder', 'totalParticipantCount', 'createdAt'],
    where: {
      id: 156
    },
    include: [{
      model: Participant,
      attributes: ['name', 'email', 'order', 'role', 'signedAt'],
      include: [{
        model: ParticipantAuth,
        attributes: ['type', 'question', 'answer', 'authAt']
      }, {
        model: ParticipantInput,
        attributes: ['inputType', 'value', 's3Key']
      }]
    }]
  }).then(function(contract) {
    console.log(contract.get({plain: true}));
    // 이력 정보 만들기
  });
};


function makeHistoryArray(contract) {
  var historyArray = [],
    participants = contract.participants,
    participantLength = participants.length,
    writer = getWriter(participants);

  historyArray.push(makeUploadHistory());
  historyArray.push(makeStartSignatureHistory());
  historyArray.concat(makeRequestSignatureHistoryArray());
  // TODO - 인증 실패 이력




  // 업로드 이력 만들기
  function makeUploadHistory() {
    return {
      title: writer.name + '(' + writer.email + ')님이 ' + contract.title + '을 업로드 하였습니다',
      time: contract.createdAt
    };
  }

  // 서명 진행시작 알림 이력 만들기
  function makeStartSignatureHistory() {
    return {
      title: '요청자(이메일)님이 문서의 서명 요청을 시작하였습니다.',
      time: contract.createdAt
    };
  }

  // 서명 요청 이력 만들기
  function makeRequestSignatureHistoryArray() {
    var i, length = participantLength,
      currentParticipationOrder = contract.currentParticipationOrder,
      requestSignatureHistoryArray = [];

    // 현재 서명참여 순서 이전의 모든 `서명인`에 해당
    for (i = 0; i < length; i += 1) {
      if (participants[i].role === '서명인' && participants[i].order <= currentParticipationOrder) {
        requestSignatureHistoryArray.push({
          title: writer.name + '(' + writer.email + ')님이 ' +
          participants[i].name + '(' + participants[i].email + ')님에게 문서의 서명을 요청하였습니다.',
          time: participants[i].requestedAt
        });
      }
    }
    return requestSignatureHistoryArray;
  }

  // 인증 성공 이력 만들기
  function makeAuthSuccessHistoryArray() {
    var i, length = participantLength;
    

  }

}

/**
 * 참여자 배열에서 작성인을 찾아서 반환하는 메서드
 *
 * @param participants - 참여자 배열
 * @returns {*}
 */
function getWriter(participants) {
  var i, length = participants.length;

  for (i = 0; i < length; i += 1) {
    if (participants[i].role === '작성인') {
      return participants[i];
    }
  }
}
